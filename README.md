'=============================================================
' Module: PAx_API_Report
' Purpose: Call IBM Planning Analytics (TM1/PAx) REST API
'          and populate Excel report automatically
'=============================================================

Option Explicit

' ---- CONFIGURATION — update these for your Assurant env ----
Private Const PA_BASE_URL   As String = "https://<your-tm1-server>:<port>/api/v1"
Private Const TM1_USER      As String = "your_username"
Private Const TM1_PASS      As String = "your_password"   ' or use Windows/CAM auth
Private Const CUBE_NAME     As String = "FixedAssets"      ' your target cube
Private Const VIEW_NAME     As String = "Default"          ' or your named view
Private Const OUTPUT_SHEET  As String = "Report"
' ---------------------------------------------------------------

'--------------------------------------------------------------
' MAIN ENTRY POINT — called by Power Automate Desktop
'--------------------------------------------------------------
Public Sub RunPAxReport()
    On Error GoTo ErrHandler
    
    Dim sToken   As String
    Dim sData    As String
    
    Application.ScreenUpdating = False
    Application.Calculation = xlCalculationManual
    
    ' Step 1: Authenticate and get session token
    sToken = GetPAxSessionToken()
    If sToken = "" Then
        MsgBox "Authentication failed. Check credentials/URL.", vbCritical
        Exit Sub
    End If
    
    ' Step 2: Fetch cube view data
    sData = GetCubeViewData(sToken, CUBE_NAME, VIEW_NAME)
    
    ' Step 3: Parse JSON and write to Excel
    If sData <> "" Then
        ParseAndWriteData sData
    End If
    
    ' Step 4: Clean up session
    LogoutPAx sToken
    
    ' Step 5: Save workbook
    ThisWorkbook.Save
    
    Application.Calculation = xlCalculationAutomatic
    Application.ScreenUpdating = True
    
    MsgBox "PAx Report refreshed successfully: " & Now(), vbInformation
    Exit Sub

ErrHandler:
    Application.Calculation = xlCalculationAutomatic
    Application.ScreenUpdating = True
    MsgBox "Error " & Err.Number & ": " & Err.Description, vbCritical
End Sub


'--------------------------------------------------------------
' STEP 1: Authenticate — returns session token or ""
' Uses Basic Auth (works for Native/TM1 auth mode)
' For CAM/LDAP, swap Authorization header accordingly
'--------------------------------------------------------------
Private Function GetPAxSessionToken() As String
    Dim oHttp As Object
    Dim sUrl  As String
    Dim sAuth As String
    
    Set oHttp = CreateObject("MSXML2.XMLHTTP.6.0")
    
    sUrl = PA_BASE_URL & "/Servers"   ' lightweight endpoint to validate auth
    
    ' Base64-encode credentials
    sAuth = "Basic " & Base64Encode(TM1_USER & ":" & TM1_PASS)
    
    oHttp.Open "GET", sUrl, False
    oHttp.setRequestHeader "Authorization", sAuth
    oHttp.setRequestHeader "Content-Type", "application/json"
    oHttp.setRequestHeader "Accept", "application/json"
    oHttp.Send
    
    If oHttp.Status = 200 Then
        ' Extract TM1SessionId from Set-Cookie header
        Dim sCookie As String
        sCookie = oHttp.getResponseHeader("Set-Cookie")
        GetPAxSessionToken = ExtractSessionId(sCookie)
    Else
        GetPAxSessionToken = ""
        Debug.Print "Auth failed: HTTP " & oHttp.Status & " - " & oHttp.responseText
    End If
    
    Set oHttp = Nothing
End Function


'--------------------------------------------------------------
' STEP 2: GET cube view data as JSON string
'--------------------------------------------------------------
Private Function GetCubeViewData(sToken As String, sCube As String, sView As String) As String
    Dim oHttp As Object
    Dim sUrl  As String
    
    Set oHttp = CreateObject("MSXML2.XMLHTTP.6.0")
    
    ' PAx REST endpoint for cube view cell values
    sUrl = PA_BASE_URL & "/Cubes('" & sCube & "')/Views('" & sView & "')/tm1.Execute?$expand=Axes($expand=Hierarchies($select=Name)),Cells($select=Value,Ordinal)"
    
    oHttp.Open "GET", sUrl, False
    oHttp.setRequestHeader "Cookie", "TM1SessionId=" & sToken
    oHttp.setRequestHeader "Accept", "application/json"
    oHttp.Send
    
    If oHttp.Status = 200 Then
        GetCubeViewData = oHttp.responseText
    Else
        GetCubeViewData = ""
        Debug.Print "Data fetch failed: HTTP " & oHttp.Status
        Debug.Print oHttp.responseText
    End If
    
    Set oHttp = Nothing
End Function


'--------------------------------------------------------------
' STEP 3: Parse JSON response and write to Report sheet
' Uses simple string parsing (no external JSON lib needed)
'--------------------------------------------------------------
Private Sub ParseAndWriteData(sJson As String)
    Dim ws      As Worksheet
    Dim oJson   As Object
    Dim oCells  As Object
    Dim oCell   As Object
    Dim iRow    As Long
    Dim iCol    As Long
    Dim dVal    As Variant
    
    Set ws = ThisWorkbook.Worksheets(OUTPUT_SHEET)
    ws.Cells.Clear
    
    ' Use ScriptControl or late-bind JScript for JSON parsing
    ' This approach uses MSXML for XML-compatible JSON parsing
    Set oJson = CreateObject("ScriptControl")
    oJson.Language = "JScript"
    
    ' Inject a simple JSON parse helper
    oJson.ExecuteStatement "var data = " & sJson & ";"
    oJson.ExecuteStatement "var cells = data.Cells;"
    oJson.ExecuteStatement "var axisRows = data.Axes[0].Hierarchies;"
    oJson.ExecuteStatement "var axisCols = data.Axes[1].Hierarchies;"
    
    ' Write headers from axes
    Dim nCols As Long
    nCols = oJson.Eval("axisCols.length")
    Dim nRows As Long
    nRows = oJson.Eval("axisRows.length")
    
    ' Write column headers (row 1)
    For iCol = 0 To nCols - 1
        ws.Cells(1, iCol + 2).Value = oJson.Eval("axisCols[" & iCol & "].Name")
        ws.Cells(1, iCol + 2).Font.Bold = True
    Next iCol
    
    ' Write row headers and cell values
    Dim nCellCount As Long
    nCellCount = oJson.Eval("cells.length")
    
    Dim cellIdx As Long
    cellIdx = 0
    
    For iRow = 0 To nRows - 1
        ' Row label (column A)
        ws.Cells(iRow + 2, 1).Value = oJson.Eval("axisRows[" & iRow & "].Name")
        ws.Cells(iRow + 2, 1).Font.Bold = True
        
        ' Cell values
        For iCol = 0 To nCols - 1
            If cellIdx < nCellCount Then
                dVal = oJson.Eval("cells[" & cellIdx & "].Value")
                ws.Cells(iRow + 2, iCol + 2).Value = dVal
                cellIdx = cellIdx + 1
            End If
        Next iCol
    Next iRow
    
    ' Auto-fit columns
    ws.Columns.AutoFit
    
    ' Timestamp
    ws.Cells(1, 1).Value = "Last Refreshed:"
    ws.Cells(1, 1).Font.Bold = True
    ws.Cells(2, 1).Value = Now()
    ws.Cells(2, 1).NumberFormat = "yyyy-mm-dd hh:mm:ss"
    
    Set oJson = Nothing
    Set ws = Nothing
End Sub


'--------------------------------------------------------------
' STEP 4: Logout / kill session
'--------------------------------------------------------------
Private Sub LogoutPAx(sToken As String)
    Dim oHttp As Object
    Set oHttp = CreateObject("MSXML2.XMLHTTP.6.0")
    oHttp.Open "POST", PA_BASE_URL & "/ActiveSession/tm1.Close", False
    oHttp.setRequestHeader "Cookie", "TM1SessionId=" & sToken
    oHttp.Send
    Set oHttp = Nothing
End Sub


'--------------------------------------------------------------
' HELPER: Extract TM1SessionId from Cookie header string
'--------------------------------------------------------------
Private Function ExtractSessionId(sCookie As String) As String
    Dim parts() As String
    Dim p       As String
    
    parts = Split(sCookie, ";")
    For Each p In parts
        p = Trim(p)
        If Left(p, 13) = "TM1SessionId=" Then
            ExtractSessionId = Mid(p, 14)
            Exit Function
        End If
    Next p
    ExtractSessionId = ""
End Function


'--------------------------------------------------------------
' HELPER: Base64 encode string (no external dependency)
'--------------------------------------------------------------
Private Function Base64Encode(sInput As String) As String
    Dim oXML  As Object
    Dim oNode As Object
    
    Set oXML = CreateObject("MSXML2.DOMDocument.6.0")
    Set oNode = oXML.createElement("b64")
    oNode.dataType = "bin.base64"
    oNode.nodeTypedValue = CreateObject("System.Text.ASCIIEncoding").GetBytes_4(sInput)
    Base64Encode = Replace(oNode.Text, vbLf, "")
    
    Set oNode = Nothing
    Set oXML = Nothing
End Function
