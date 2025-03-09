## PBIP Format - Power BI Projects

Source: [Projects Overview - PBIP](https://learn.microsoft.com/en-us/power-bi/developer/projects/projects-overview)

>  When you save your work as a Power BI Project (PBIP), report and semantic model item definitions are saved as individual plain text files in a simple, intuitive folder structure.

Saving your work as a project has the following benefits:

- Text editor support
- Programmatic generation and editing item definitions
- Source control (Git)
- Continuous Integration and Continuous Delivery (CI/CD) - GitHub .Actions / Azure DevOps


## .gitIgnore


```
**/.pbi/localSettings.json
**/.pbi/cache.abf
```

### <project name>.pbip

The PBIP file contains a pointer to a report folder, opening a PBIP opens the targeted report and model for authoring.


### Considerations and limitations

Power BI Desktop isn't aware of changes made with other tools or applications. Changes made by using external tools require you to restart Power BI Desktop before those changes are shown.
Sensitivity labels aren't supported with Power BI projects.
Diagram view is ignored when editing models in the Service.
When saving as a Power BI Project, the maximum length of the project files path is 260 characters.
In Power BI Desktop, you can't save as a PBIP directly to OneDrive and SharePoint. You can use Save As to save files to a locally synced OneDrive folder; however, this may cause file locking issues, potentially leading to failed save operations in Power BI Desktop.
When editing PBIP files outside of Power BI Desktop, they should be saved using UTF-8 without BOM encoding.
Report Linguistic Schema isn't supported with Power BI projects.
Power BI Desktop uses CRLF as end-of-line. To avoid problems in your diffs, configure Git to handle line endings by enabling autocrlf.
Power BI Projects is currently not supported in Microsoft Power BI Desktop version optimized for Power BI Report Server.

### Frequently asked questions

Question: Looking at semantic model and report item folder definitions only a few files are marked as required, what happens if I delete them?
Answer: Power BI Desktop automatically creates them when you save as a project (PBIP).

Question: Is Power BI Desktop aware of changes I make to the Power BI Project files from an external tool or application?
Answer: No. Any change made to the files requires Power BI Desktop to be restarted to reflect those changes.

Question: If I convert a PBIX to a PBIP, can I convert it back to PBIX?
Answer: Yes. You can save a PBIX as a PBIP, or save a PBIP as a PBIX.

Question: Can I convert PBIX into PBIP and vice-versa programmatically?
Answer: No. You can only convert a PBIX into a PBIP and vice-versa using Power BI Desktop's File > Save as.

Question: Can I deploy a Power BI Desktop project to Azure Analysis Services (AAS) or SQL Server Analysis Services (SSAS)?
Answer: No. Power BI Desktop project report definitions aren't supported in AAS and SSAS. And model definitions use an enhanced metadata unique to Power BI. For AAS and SSAS projects, use Microsoft Visual Studio for model authoring, Git, and Azure DevOps integration.

Question: Why isn't there a *.pbip file when I connect my Fabric workspace to Git? How can I edit my report and semantic model in Power BI Desktop?
Answer: The PBIP file is optional and simply serves as a shortcut to the report folder. You can open both the report and the semantic model for editing in Power BI Desktop by opening the definition.pbir file located in the report folder.


### TMDL Format - 

Tabular Model Scripting Language (TMSL)

Tabular Model Definition Language (TMDL)


> With the objective of providing a better source control and co-development experience, you can save your Power BI Project files (PBIP) using Tabular Model Definition Language (TMDL) as the semantic model file format.
> Unlike Tabular Model Scripting Language (TMSL), TMDL has been designed from the ground up to be human-friendly, facilitating not only readability but also easy editing in any text editor. This represents a substantial enhancement for source control and collaborative development experiences.


Source: https://learn.microsoft.com/pt-br/power-bi/developer/projects/projects-dataset#tmdl-format

![TMSL x TMDL](https://learn.microsoft.com/en-us/power-bi/developer/projects/media/projects-dataset/semantic-model-compare.png)  

For a better experience reading and editing your TMDL files, install the [TMDL - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=analysis-services.TMDL) Microsoft VS Code extension.

Open the PBIP folder using VS Code and navigate to semantic model definition folder.


### PBIR Format - Power BI Desktop project report folder

Source: https://learn.microsoft.com/pt-br/power-bi/developer/projects/projects-report#pbir-format

#### definition.pbir

Contains the overall definition of a report and core settings. This file also holds the reference to the semantic model used by the report. Power BI Desktop can open a pbir file directly, just the same as if the report were opened from a pbip file. Opening a pbir also opens the semantic model alongside if there's a relative reference using byPath.

Example definition.pbir:

```json
{
  "version": "1.0",
  "datasetReference": {
    "byPath": {
      "path": "../Sales.Dataset"
    },
    "byConnection": null
  }
}
```

The definition includes the datasetReference property, which references the semantic model used in the report. The reference can be either:

byPath - Specifies a relative path to the target semantic model folder. Absolute paths aren't supported. A forward slash (/) is used as a folder separator. When used, Power BI Desktop also opens the semantic model in full edit mode.

byConnection - Specifies a remote semantic model in the Power BI service by using a connection string. When a byConnection reference is used, Power BI Desktop doesn't open the semantic model in edit mode.

Using a byConnection reference, the following properties must be specified:

|Property|Description|
|:--|:--|
|connectionString|The connection string referring to the remote semantic model.|
|pbiModelDatabaseName|The remote semantic model ID.|
|connectionType	Type of connection.|For service remote semantic model, this value should be pbiServiceXmlaStyleLive.|
|pbiModelVirtualServerName|An internal property that should have the value, sobe_wowvirtualserver.|  


Example using byConnection:  



```json
{
  "version": "1.0",
  "datasetReference": {
    "byPath": null,
    "byConnection": {
      "connectionString": "Data Source=powerbi://api.powerbi.com/v1.0/myorg/[WorkpaceName];Initial Catalog=[SemanticModelName];Integrated Security=ClaimsToken",
      "pbiServiceModelId": null,
      "pbiModelVirtualServerName": "sobe_wowvirtualserver",
      "pbiModelDatabaseName": "[Semantic Model Id]",
      "connectionType": "pbiServiceXmlaStyleLive",
      "name": "EntityDataSource"
    }
  }
}
```


Saving your Power BI Project files (PBIP) using the Power BI Enhanced Report Format (PBIR) greatly improves change tracking and merge conflict resolution by using properly formatted JSON files.  


Each page, visual, bookmark, etc., is organized into a separate, individual file within a folder structure. This format is ideal for co-development conflict resolution.  

Unlike PBIR-Legacy (report.json), PBIR is a publicly documented format that supports modifications from non-Power BI applications. Each file has a public JSON schema, which not only documents the file but also lets code editors like Visual Studio Code perform syntax validation while editing.

Some of the possible scenarios now available with PBIR include:

Copy pages/visuals/bookmarks between reports.
Ensure consistency of a set of visuals across all pages, by copying & pasting the visual files.
Easy find and replace across multiple reports files.
Apply a batch edit across all visuals using a script (for example, hide visual level filters)


The existing PBIR-Legacy file (report.json) is replaced with a \definition folder containing the PBIR representation of the report.


PBIR folder and files  


PBIR naming convention
All names inside the square brackets ([]) in the preceding table follow a default naming convention but can be renamed to more user-friendly names. By default, pages, visuals, and bookmarks use their report object name as their file or folder name. These object names are initially a 20-character unique identifier, such as '90c2e07d8e84e7d5c026'.


