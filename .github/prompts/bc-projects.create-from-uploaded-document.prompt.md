---
agent: 'agent'
description: 'Create a Business Central project from a document uploaded into Business Central.'
---

# BC Project from Uploaded Document

Your goal is to create a new project in Business Central based on a document that has already been uploaded into Business Central (for example Word, text, XML, or Excel).

## Inputs

- Uploaded document name: ${input:filename:Name (or partial name) of the uploaded document in Business Central}
- Preferred project name/description: ${input:projectDescription:Short description to use on the project}
- Customer hint (optional): ${input:customerHint:Known customer name/number, if any}

## Workflow

1. **Preparation**
   - Read .github/instructions/copilot-instructions.md and .docs/Business_Central_API_Guide.md to refresh the Uploaded Project Document workflow and critical rules.

2. **Locate candidate documents**
   - Use the ListProjectDocuments MCP tool to:
     - Retrieve the most recently uploaded project documents.
     - Filter by ${input:filename} (name contains or equals) to get a small candidate list.
   - Present the candidates (document name, type, projectNo if any, uploaded timestamp) and ask the user to confirm which document to use before continuing.

3. **Retrieve content**
   - If the document is Word, text, or XML:
     - Use ListProjectDocumentLines to retrieve the text/html lines and concatenate them into the full document content (preserving order).
   - If the document is Excel:
     - Use ListExcelSheets to identify relevant sheet(s).
     - Use ListExcelSheetCells to retrieve and reconstruct the sheet content for analysis.

4. **Classify and analyze**
   - Classify the document content:
     - Formal project plan (WBS-style)?
     - RFP / proposal / requirements? 
     - Budget or estimate spreadsheet?
   - If it is clearly an MS Project-style export, treat it similarly to the XML workflow (WBS is source of truth).
   - Otherwise, follow the Uploaded Project Document / DOCX-style workflow from copilot-instructions.md:
     - Identify phases, functional areas, deliverables, and constraints.
     - Propose a WBS-based task structure with clear phases and child tasks.
     - Mark which tasks will be Posting vs Begin-/End-Total, and where planning lines will exist.
   - Show the proposed structure and get explicit user approval before creating any records.

5. **Customer resolution**
   - Infer the intended customer from the document or from ${input:customerHint}.
   - Use Customers MCP tools to search and present likely matches; ask the user to confirm or request a new customer.
   - If needed, create a new customer following Business_Central_API_Guide.md and number series rules.

6. **Project creation**
   - Create the project (Job) via Projects MCP tools:
     - Leave `number` blank unless automatic numbering is unavailable.
     - Use ${input:projectDescription} or a refined description.
     - Set `startingDate` and `endingDate` from the document, or confirm with the user if unclear.

7. **Task hierarchy and planning lines**
   - Convert the approved WBS into Job Tasks using the Job Task Numbering Strategy from the API guide (zero-padded, ASCII-safe, indentation from WBS).
   - For any task with children, create matching Begin-Total and End-Total tasks as described in the API guide.
   - Create Posting tasks for executable work items only.
   - For resource-related tasks, create planning lines (type = Resource) in line with copilot-instructions.md, using durations/effort from the document where possible.

8. **Attach the document**
   - Use the ModifyProjectDocuments MCP tool to link the original uploaded document to the newly created project for future reference.

9. **Review**
   - Summarize the created project (project number, description, task count, planning lines) and confirm with the user that it matches expectations.

This workflow implements Sample Prompt 2: "I have uploaded a new file in Business Central named #filename..." and standardizes how uploaded documents are analyzed and turned into projects.
