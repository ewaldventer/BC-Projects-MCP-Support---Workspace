---
agent: 'agent'
description: 'Create or update Business Central resources and their skills (competencies) for project work.'
---

# BC Resource & Skill Setup

Your goal is to create or update resources (including placeholders) and assign appropriate skills so that they can be used effectively in project planning lines.

## Inputs

- Resource display name: ${input:resourceName:Name of the resource to create or update (for example, "Senior Developer")}
- Is this a new placeholder resource?: ${input:isPlaceholder:yes/no}

## Workflow

1. **Preparation**
   - Read .github/instructions/copilot-instructions.md and .docs/Business_Central_API_Guide.md, focusing on:
     - Resources creation (request body template and number series rules).
     - Competencies (resource skills) and the requirement to use `resourceNo` (not GUID).

2. **Locate existing resource (if any)**
   - Use Resources MCP tools to search for resources whose `name` or description matches ${input:resourceName}.
   - If matches are found:
     - Present them to the user (number, name, type, placeholder flag).
     - Ask whether to reuse one of them or to create a new resource instead.

3. **Create a new resource (Sample Prompt 5)**
   - If a new resource is requested (for example, a placeholder named "Senior Developer"):
     - First attempt to Invoke the Resources creation from the config template from Business_Central_API_Guide.md, then use ModifyResource to update the fields as needed.
     - If templates are not available, create the resource using Resources MCP tools with:
       - `number`: leave blank to allow number series to assign, unless Business Central requires an explicit value.
       - `type`: Person (for consultants/people).
       - `name`: ${input:resourceName}.
       - `isPlaceholder`: set from ${input:isPlaceholder}.
       - `baseUnitOfMeasure`: typically `HOUR`.
       - Optionally set `unitCost` and `unitPrice` if specified (for example, unit cost 1500, unit price 2000 as in Sample Prompt 5).
     - Confirm all details with the user before sending the create call.

4. **Identify or create skill codes**
   - Use SkillCodes MCP tools to list existing skill codes relevant to the resource, such as:
     - CSIDE/AL Developer
     - C# Developer
     - Java Developer
     - PowerShell Scripting
     - TypeScript
     - HTML and CSS
     - T-SQL
     - Business Central Functional Consulting
     - Pascal Developer
     - VB Developer
     - Hardware Programming
   - For any missing skill codes, ask the user whether to create them, then create using SkillCodes MCP tools with clear, descriptive `code` and `description` values.

5. **Assign competencies (Sample Prompt 6)**
   - Once you have the resource `number` and the list of desired skill codes:
     - For each skill, use Competencies MCP tools to create a competency record with:
       - `resourceNo`: the resource number (for example, the number for ${input:resourceName}).
       - `skillCode`: the chosen skill code.
   - Avoid duplicates by checking existing competencies for this resource before creating new ones.

6. **Review and confirmation**
   - Summarize the final resource setup:
     - Resource number, name, placeholder status, unit cost and price.
     - Assigned skill codes.
   - Confirm with the user that this matches the intended role profile and is ready to be used on planning lines.

This workflow implements Sample Prompts 5 and 6 by standardizing how resources (like a placeholder "Senior Developer") and their skills are created and maintained for project planning.
