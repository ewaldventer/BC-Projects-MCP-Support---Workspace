---
agent: 'agent'
description: 'Review and manage project planning lines, including splitting by capacity and assigning resources.'
---

# BC Project Planning Line Management

Your goal is to review and adjust project planning lines for a given project, including splitting lines over time, assigning suitable resources, and preparing changes for user approval.

## Inputs

- Project number: ${input:projectNo:Project number to work with (for example, PR00170)}

## Workflow

1. **Preparation**
   - Read .github/instructions/copilot-instructions.md and .docs/Business_Central_API_Guide.md, focusing on:
     - Planning Line Configuration and Resource Type Matching.
     - Resource Capacity and Line Splitting.
     - Employee/Resource Absences (read-only) and capacity formula.

2. **Retrieve current planning lines**
   - Use the ProjectPlanningLines MCP tools (ListProjectPlanningLines) to fetch all planning lines for ${input:projectNo}.
   - Group lines by task, resource, and line type (Budget, Billable, Both) and present a concise overview to the user.

3. **Split lines by capacity (Sample Prompt 3)**
   - For lines that represent multi-day effort (for example, Quantity >> daily capacity):
     - Use ResourceCapacity MCP tools to determine daily available capacity for each resource in the relevant date range.
     - If capacity records are missing, pause and ask the user whether they want to adjust capacity in Business Central before continuing.
   - Propose a splitting plan per planning line, for example:
     - 16 hours task, 4 hours/day capacity → 4 lines of 4 hours on consecutive working days.
   - Present the proposed split for each line and wait for user confirmation.
   - When approved, either:
     - Create new planning lines manually and adjust the original, or
     - Use the SplitLine action (where appropriate) on the planning line MCP tools, ensuring quantities remain consistent.

4. **Assign best candidate resources (Sample Prompt 7)**
   - For planning lines on ${input:projectNo} without a specific resource, or where resource assignment should be reviewed:
     - Identify required skills or roles from the planning line descriptions and related tasks.
     - Use Resources and Competencies MCP tools to:
       - Find resources with matching skill codes.
       - Check their capacity/availability (using ResourceCapacity and EmployeeAbsences) during the planned period.
   - For each planning line, propose one or more candidate resources with rationale:
     - Skill match.
     - Availability and capacity.
     - Existing load on other projects.
   - Present these proposals to the user without changing data yet.

5. **Apply approved resource assignments**
   - After the user approves the proposed assignments, update the planning lines:
     - Use ProjectPlanningLines MCP tools to set the chosen `number` (resource) on each line.
     - Preserve the existing `quantity` when changing resources, as described in Business_Central_API_Guide.md (include `quantity` in PATCH requests).
   - Summarize the changes applied (which lines, old vs new resources).

6. **Optional: configuration templates (Sample Prompt 4 reference)**
   - If the user wants to standardize new lines:
     - Use ConfigTemplates MCP tools to list relevant templates.
     - Suggest using templates for consistent planning line creation in future tasks.

7. **Final review**
   - Provide a final summary covering:
     - Lines split and resulting schedule.
     - New or updated resource assignments.
     - Any lines left unchanged and reasons (for example, missing capacity setup or unclear skills).

This workflow consolidates Sample Prompts 3, 4, and 7: splitting planning lines, considering templates, and assigning best-fit resources based on skills and capacity, always with user confirmation before committing changes.
