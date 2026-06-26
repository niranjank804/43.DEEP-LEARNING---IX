Hi Lisa,

I wanted to share a summary of some research I completed on IBM Bob, IBM's AI-first software development platform (GA: April 28, 2026), and its potential applicability to our Planning Analytics / TM1 environment.

After reviewing IBM's public documentation and evaluating the platform from a Planning Analytics perspective, my assessment is that IBM Bob has strong potential to improve the development lifecycle around TM1, although it is not currently a replacement for experienced TM1 engineering expertise.

**Areas where IBM Bob adds value for PA teams**

* **TM1py and Python automation:** Strong support for TM1py scripting, REST API automation, security audits, dimension comparisons, and ETL development.
* **Documentation:** Can generate documentation, SOPs, and change records from exported TI process files, reducing the effort required to document existing models.
* **CI/CD and SDLC:** Supports governed development workflows, audit trails, approval gates, and deployment automation through BobShell.
* **Enterprise governance:** Includes security scanning, reusable development standards, and audit capabilities that align well with enterprise change-management practices.

**Areas requiring careful review**

* **MDX query generation:** Standard MDX generation appears capable, but TM1-specific functions should be validated by an experienced developer.
* **TI process generation:** Bob can produce useful first drafts, but generated code should always be reviewed and tested in a non-production environment before deployment.

**Areas where IBM Bob does not replace TM1 expertise**

* Cube rule and feeder design
* SKIPCHECK and performance optimization
* Security cube architecture
* PA Cloud administration, including thread analysis, memory diagnostics, lock contention, and chore management

These remain architectural and operational responsibilities that require experienced Planning Analytics practitioners.

**Licensing observations**

* Free Trial: 30 days / 40 BobCoins
* Pro: $20/month (40 BobCoins)
* Pro Plus: $60/month (160 BobCoins) – appears to be the most practical tier for regular development work
* Ultra: $200/month (500 BobCoins) – intended for larger modernization initiatives

One development that I believe is particularly interesting is the introduction of **Planning Analytics MCP tool servers (PA 3.1.4+)**. These endpoints provide a standards-based interface that enables MCP-compatible AI clients to interact with Planning Analytics objects, execute MDX, and manage TI processes. This could become an important capability as AI-assisted development continues to evolve.

Overall, I view IBM Bob as a strong enterprise SDLC and governance platform for Planning Analytics development rather than a TM1-specific expert assistant. It appears particularly valuable for Python automation, documentation, and deployment workflows, while core TM1 model design and production changes should continue to rely on experienced developer review.

I have also prepared a more detailed technical assessment that includes architecture diagrams, comparison matrices, implementation considerations, and references. I'd be happy to share it or discuss any of the findings if you're interested.

Best regards,

Niranjan Patra
Senior TM1 / Planning Analytics Consultant


