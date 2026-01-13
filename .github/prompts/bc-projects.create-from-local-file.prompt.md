---
agent: 'agent'
description: 'Create a Business Central project from a local project document (XML or DOCX).'
---

# BC Project from Local File

Your goal is to create a new project in Business Central based on a project definition file that exists in this workspace.

## Inputs

- Project document path: ${input:filename:Path to the project file (XML or DOCX)}
- Preferred project name/description: ${input:projectDescription:Short description to use on the project}
- Customer hint (optional): ${input:customerHint:Known customer name/number, if any}

## Workflow

1. **Preparation**
   - Read .github/instructions/copilot-instructions.md and .docs/Business_Central_API_Guide.md to refresh workflows and critical rules (number series, Job Task Numbering, Begin-/End-Total, planning lines, resource handling).
   - Confirm file type (XML vs DOCX) from ${input:filename}.

2. **If XML (Microsoft Project export)**
   - Use the XML Parsing workflow from copilot-instructions.md:
     - Parse the XML (you may use a temporary PowerShell script in a tmp folder as shown in the guide).
     - Extract project information, WBS, tasks, resources, and dates.
     - Summarize key findings (project name, start/end dates, main phases, number of tasks and resources).
     - Present a concise WBS summary and ask the user to confirm that it matches their plan before creating anything in Business Central.

3. **If DOCX (RFP/proposal)**
   - Use the DOCX Parsing workflow from copilot-instructions.md:
     - Extract text from the DOCX.
     - Identify phases, functional areas, and deliverables.
     - Propose a WBS-based task structure (with clear phases and sub-tasks).
     - Call out which tasks will be Posting vs Begin-/End-Total and where planning lines will be needed.
     - Ask the user to confirm or adjust the proposed structure before creating anything in Business Central.

4. **Customer resolution**
   - From the confirmed structure and document content, infer the intended customer.
   - Use Business Central Customers MCP tools to:
     - Search for likely matching customers.
     - Show candidates and ask the user to confirm the correct customer, or confirm that you should create a new customer.
   - If a new customer is needed, create it following Business_Central_API_Guide.md (respect number series rules).

5. **Project creation**
   - Create the project (Job) via Projects MCP tools:
     - Leave `number` blank unless Business Central reports that automatic numbering is not possible.
     - Use ${input:projectDescription} or a refined description from the document (shorten it to 100 characters).
     - Set `startingDate` and `endingDate` based on the document or user confirmation.

6. **Task hierarchy (WBS → Job Tasks)**
   - Derive Job Task No. from the WBS using the Job Task Numbering Strategy in Business_Central_API_Guide.md (zero-padded, ASCII-safe).
   - Set indentation from the WBS (period count).
   - For any task with children, create a Begin-Total task and a matching End-Total task that sorts after its children, following the detailed Begin-/End-Total workflow in the API guide.
   - Create Posting tasks for leaf-level work items only.

7. **Planning lines**
   - For tasks that represent actual work, create planning lines based on durations, work, or effort in the source document.
   - Use `type = Resource` for resource activities (as per copilot-instructions.md).
   - If specific resource names are present, try to match them to existing resources; otherwise, propose placeholder resources and wait for user approval before creating new ones.

8. **Review**
   - Summarize:
     - Project number and description.
     - Number of tasks (Begin-Total, Posting, End-Total).
     - Number and types of planning lines.
   - Ask the user to validate that the created structure matches expectations and suggest next logical actions (for example, billing setup or resource refinement).

This workflow implements Sample Prompt 1: "Create a new project in Business Central based on #filename" while enforcing the Business Central Projects rules and human confirmation at key steps.
