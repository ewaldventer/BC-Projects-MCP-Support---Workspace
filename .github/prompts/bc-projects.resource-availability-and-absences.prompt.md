---
agent: 'agent'
description: 'Analyze resource availability, including absences and existing project commitments, and propose alternatives.'
---

# BC Resource Availability & Absences

Your goal is to analyze resource availability for upcoming project work, considering absences and current workload, and to propose alternative resources where needed.

## Inputs

- Time horizon (days): ${input:horizonDays:How many days ahead to analyze (for example, 21)}
- Optional project number: ${input:projectNo:Project number to focus on (optional)}

## Workflow

1. **Preparation**
   - Read .github/instructions/copilot-instructions.md and .docs/Business_Central_API_Guide.md, focusing on:
     - Employee/Resource Absences (read-only) and their fields.
     - ResourceCapacity and the capacity formula.
     - The note about converting absence `quantity` into the same unit used by capacity (hours vs days).

2. **Identify resources to analyze**
   - If ${input:projectNo} is provided:
     - Use ProjectPlanningLines MCP tools to identify all resources referenced on planning lines for that project.
   - If no project is provided:
     - Ask the user to specify one or more resources or roles to analyze, or default to all active resources.

3. **Collect capacity and absences (Sample Prompt 8)**
   - For each resource in scope:
     - Use Resources MCP tools to confirm whether it is linked to an employee (employeeNo).
     - Use EmployeeAbsences MCP tools to query absences for the next ${input:horizonDays} days.
     - Use ResourceCapacity MCP tools to retrieve available capacity for the same date range.
   - For each day in the horizon, compute:
     - Available capacity = Total capacity − Planned work (if known) − Absence quantity (after converting units to match capacity).

4. **Current workload and conflicts**
   - Use ProjectPlanningLines and ProjectLedgerEntries MCP tools to estimate planned and actual workload for each resource in the horizon:
     - Planned work from planning lines.
     - Already posted work from ledger entries.
   - Flag any days or periods where:
     - Available capacity is zero or negative.
     - There is a significant risk of over-allocation.

5. **Propose alternative resources**
   - For resources that will be unavailable or over-allocated during the horizon:
     - Use Competencies and SkillCodes MCP tools to find other resources with similar skills.
     - Check their absences and capacity over the same horizon.
   - Propose alternative resources per task or planning line, with rationale:
     - Skill match.
     - Availability.
     - Reduced risk of conflict.
   - Present proposals without changing any assignments yet.

6. **Optional updates**
   - If the user approves certain substitutions or schedule changes:
     - Use ProjectPlanningLines MCP tools to update resource assignments and/or planning dates.
     - When changing the resource on a planning line, preserve `quantity` as described in Business_Central_API_Guide.md.

7. **Summary**
   - Provide a clear summary including:
     - Which resources have absences that affect the next ${input:horizonDays} days.
     - Where over-allocation exists.
     - Suggested alternative resources and any approved changes applied.

This workflow implements Sample Prompt 8 and extends it with a structured capacity and absence review so that resource assignment decisions are made with full visibility of constraints.
