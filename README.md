Thanks for clarifying.
My understanding is that the cleaner approach is to have the allocation method driven by the configured source center rather than storing it on every recipient center.
My plan is to read the Grp Ins Alloc Mthd attribute from the source center once in the Prolog and store it in a variable. That value will then control the calculation method for the entire allocation run, so all recipient centers will use the same method. The attribute would therefore only need to be maintained on the source center.
Does that align with the intended design?
