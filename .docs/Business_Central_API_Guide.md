# Scope

This document provides an overview of the Business Central API, including its features, endpoints, and usage guidelines. It is intended for developers,  technical users and MCP tools who want to integrate with Business Central using its API.

## Glossary 📖

- The terms Project and Job are used interchangeably in Business Central.

## Tools

- As far as possible, use the Business Central MCP Server to perform API calls instead of writing scripts.

## Numbering 🔢

### ⚠️ CRITICAL: Always Follow Number Series Auto-Generation

**DO NOT make assumptions about numbering.** The instructions in the Numbering section below are binding and must be followed for all resource, customer, and project creation.


- Business Central provides number series (No. Series) functionality. 
- This means that when creating a project, you can leave the number blank as it will read the setup from the Projects Setup (Jobs Setup) and generate the next number automatically.
- Exception: If you get an error: "It is not possible to assign numbers automatically", then you need to explicitly provide a number. 
  - First suggest a number and confirm with the user to accept the suggestion or provide an alternative number before proceeding.
- The same applies when creating:
  - Customers (No. Series configured on Sales & Receivables Setup)
  - Resources (No. Series configured on Resources Setup)

### ⚠️ CRITICAL: Job Task Numbering Strategy

**Business Central sorts Job Task No. by ASCII text, NOT numerically.** This causes incorrect sorting without proper zero-padding.

##### Problem Example ❌ WRONG:
```
Without padding, tasks sort incorrectly:
1
1.1
1.10      ← Wrong position! (ASCII sorts "1.10" before "1.2")
1.2
10        ← Wrong position!
2
20        ← Wrong position!
3
```

##### Solution Example ✅ CORRECT:
```
With proper padding (determined by max tasks), tasks sort correctly:
001
001.001
001.002
...
001.010   ← Correct position!
002
003
...
010       ← Correct position!
020       ← Correct position!
```

#### MANDATORY Workflow:

**Step 1: Analyze the Project Structure**
1. Count total tasks at EACH level of the WBS hierarchy
2. Determine maximum digits needed per level
3. Calculate zero-padding format that fits within 20-character limit

**Example Analysis:**
```
WBS Structure Analysis:
- Level 0 (top): Tasks 1-25 → Need 2 digits (01-25)
- Level 1 (e.g., 1.1-1.18): Max 18 subtasks → Need 2 digits (01-18)
- Level 2 (e.g., 1.1.1-1.1.5): Max 5 subtasks → Need 2 digits (01-05)

Format: XX.XX.XX (2 digits per level, 8 chars total including dots)
Max WBS "25.18.05" → Job Task No: "25.18.05" (8 chars, well under 20 limit ✅)
```

**Step 2: Convert WBS to Zero-Padded Job Task No.**

Rules:
- **Job Task No.**: Zero-padded version of WBS (for correct sorting)
- **Indentation**: Count periods in WBS (WBS "1" = 0, "1.1" = 1, "1.1.1" = 2)

**Step 3: Create Tasks**

Example conversion:
```
Original WBS: "1.10.3"
Job Task No: "01.10.03" (with 2-digit padding per level)
Job Task Description: "Design Database Schema"
Indentation: 2 (two periods in WBS)
```

#### ⚠️ CRITICAL: Use WBS as Job Task No. Foundation

**NEVER auto-generate task numbers.** ALWAYS derive Job Task No. from WBS with appropriate zero-padding.

**Workflow:**
1. Extract WBS where available.
  - If not available, suggest one based on task hierarchy and confirm with user.
2. Determine zero-padding format (see above)
3. Apply padding to WBS → Job Task No.
4. Calculate Indentation from WBS periods

#### Task Type and Structure

- **Default Type**: Job tasks should be `Posting` by default
- **Leaf tasks without children**: Create as **Planning Lines** under parent task (not as separate Job Tasks)
- **Tasks with Planning Lines**: MUST have `Type = Posting`
- **Begin-Total/End-Total**: Create logical grouping tasks with `Type = Begin-Total` and `End-Total` if not already present (refer to Business_Central_Projects_Module_Study_Guide.md)

#### Begin-Total and End-Total Task Pairing

Business Central uses **Begin-Total** and **End-Total** task types to create hierarchical groupings and enable rollup of costs and progress.

High-level rules:
- Any task that has child tasks in the WBS MUST be a **Begin-Total** task.
- Each Begin-Total task MUST have a matching **End-Total** task at the same indentation level.
- All Posting tasks that belong to a group must be sorted **between** its Begin-Total and End-Total tasks.

Recommended workflow:
1. **Identify grouping points** in the WBS:
  - For each task that has children, mark it as a Begin-Total candidate.
2. **Determine End-Total numbers** for each group:
  - For a group whose children are, for example, `01.01.01`–`01.01.03`, choose an End-Total number that sorts **after** the last child, such as `01.01.99`.
  - At a higher level, if children are `01.01`–`01.06`, use something like `01.99` as the End-Total.
3. **Match indentation**:
  - The indentation of the End-Total task MUST equal the indentation of its Begin-Total task (derived from the number of periods in the Job Task No.).
4. **Create End-Total tasks from inner to outer levels**:
  - First create End-Total tasks for the deepest level groups (e.g., `01.01.99`, `01.02.99`, ...),
  - Then for mid-level groups (e.g., `01.99`, `02.99`),
  - Finally for any overall project-level grouping, if used.

Verification checklist:
- Every Begin-Total task has a corresponding End-Total task.
- End-Total numbers sort after all Posting tasks in the group.
- Indentation matches between Begin-Total and End-Total pairs.
- Posting tasks appear between their Begin-Total and End-Total in sorted order.

#### Planning Line Creation

- Create planning lines using the **Start** and **Finish** times from the task in the XML file
- Review hierarchical structure to ensure tasks and planning lines align correctly

#### Final Review

- Verify sorting order is correct (numerically ascending)
- Verify indentation matches WBS hierarchy
- Verify all leaf tasks became planning lines under parent tasks


# Creating Records

- The order of properties (fields) are important.
- **First** see if there is a Configuration Template available for the entity you are creating. 
  - If yes, prompt the user use the template to create the record.
  - If no, use the property list below.

## Resources

### ⚠️ CRITICAL: Request Body Properties Only

**Use the below request body as template.** Only provide the properties listed. **Do not add any extra properties** unless explicitly specified by the user.

**NOTE:** Query parameters (like `select`, `filter`, etc.) are tool-level options and are separate from the request body. Only the JSON properties below should be included in the request.

### Required and Optional Properties

```json
{
  "number": "",
  "type": "Person",
  "name": "Resource Name",
  "isPlaceholder": true,
  "unitCost": 100,
  "unitPrice": 150,
  "baseUnitOfMeasure": "HOUR"
}
```

**Legend:**
- `number`: Leave blank to auto-generate (if configured in Resources Setup). If you receive "cannot assign numbers automatically" error, suggest a specific number to the user.
- `type`: REQUIRED - Either "Person" or "Machine"
- `name`: REQUIRED - Resource name
- `isPlaceholder`: OPTIONAL - Boolean (true/false) to mark as placeholder resource
- `unitCost`: OPTIONAL - Cost per unit
- `unitPrice`: OPTIONAL - Price per unit  
- `baseUnitOfMeasure`: REQUIRED - Unit of measure (e.g., "HOUR", "DAY")

### Example Usage

```json
{
  "number": "",
  "type": "Person",
  "name": "Senior Consultant",
  "baseUnitOfMeasure": "HOUR"
}
```

**DO NOT include:**
- `select` (query parameter, not part of request body)
- `filter` (query parameter, not part of request body)
- Any other custom properties

## Resource Skills / Competencies

**NOTE:** ⚠️ When referring to Resource Skills in Business Central API, the correct entity name is `competency` and the entity set name is `competencies`.

### ⚠️ CRITICAL: Request Body Properties Only

**Use the below request body as template.** Only provide the properties listed. **Do not add any extra properties** unless explicitly specified by the user.

**NOTE:** Query parameters (like `select`, `filter`, etc.) are tool-level options and are separate from the request body. Only the JSON properties below should be included in the request.

### ⚠️ CRITICAL: Use Resource Number, Not ID

**When creating competencies, you MUST use the resource `number` field, not the resource `id`.** Follow this workflow:

1. Use the Resources tool to find the resource and retrieve its `number` field (e.g., "R0350")
2. Try and find the best candidate resource by name if needed and confirm with the user
3. Use this `number` value in the `resourceNo` property of the competencies request
4. Do NOT use the `id` (GUID) field from the resource record

### Required and Optional Properties

```json
{
  "resourceNo": "R0350",
  "skillCode": "CSHARP"
}
```

**Legend:**
- `resourceNo`: REQUIRED - Resource **number** from Business Central (e.g., "R0350", not the GUID/id)
- `skillCode`: REQUIRED - Skill code to associate with the resource

### Example Usage

```json
{
  "resourceNo": "R0350",
  "skillCode": "CSHARP"
}
```

**DO NOT include:**
- `select` (query parameter, not part of request body)
- `filter` (query parameter, not part of request body)
- Any other custom properties

## Employee/Resource Absences

### Overview

Resource availability is affected by employee absences. Business Central links Resource records to Employee records, allowing you to track when resources are unavailable due to absences (vacation, sick leave, training, etc.). These absences **must be factored into resource capacity planning**.

**⚠️ READ-ONLY:** Employee Absence records are managed in Business Central's HR module and exposed through the API as **read-only**. You can query absences to inform resource capacity calculations, but cannot create or modify absence records through the API.

### Relationship: Resources ↔ Employees ↔ Absences

**Resource Record Structure:**
- Each Resource has an associated **Employee No.** field (if the resource is a person)
- Each Resource also has an **Employee ID** (GUID) field for referencing the Employee table
- Use the **Employee No.** to query employee/resource absences

**Workflow:**

1. **Find the Resource**
   - Query the Resources API to get the resource's `employeeNo` and `employeeId`
   - Example: Resource "R0350" (Ben) → Employee No. = "BEN001"

2. **Query Employee Absences (READ-ONLY)**
   - Use the `employeeNo` to query the Employee Absences API
   - Filter by `employeeNo` and date range: `employeeNo eq 'BEN001' and fromDate le 2026-02-28 and toDate ge 2026-02-01`
   - Each absence record shows: from date, to date, quantity (days), cause of absence code
   - **Note:** Absence records must be created/modified in Business Central's HR module, not through this API

3. **Adjust Resource Capacity in Project Planning**
   - When calculating available capacity for a resource on a specific date, subtract the absence quantity
   - Example: Resource has 40 hours/week availability, but has 5 days (40 hours) absence → Available capacity = 0
   - Update project planning line assignments to account for unavailable resources

### Employee Absence Record Properties (READ-ONLY)

Example response when querying absences:

```json
{
  "id": "system-id-guid",
  "employeeNo": "BEN001",
  "fromDate": "2026-02-15",
  "toDate": "2026-02-19",
  "causeOfAbsenceCode": "VACATION",
  "description": "Annual vacation",
  "quantity": 5,
  "unitOfMeasureCode": "DAY",
  "comment": "Beach trip"
}
```

**Available Fields:**
- `id`: System ID (GUID) - Absence record identifier
- `employeeNo`: The employee/resource identifier (from Resource table)
- `fromDate`: Start date of absence
- `toDate`: End date of absence (inclusive)
- `causeOfAbsenceCode`: Type of absence (VACATION, SICK, TRAINING, etc.)
- `quantity`: Number of days/hours absent
- `unitOfMeasureCode`: Unit (DAY, HOUR, etc.)
- `description`: Description of absence
- `comment`: Additional notes

**To manage absences**, use Business Central's HR module directly, not the API.

### Resource Capacity Calculation with Absences

**Formula:**
```
Available Capacity = Total Capacity - Planned Work - Absence Quantity
```

**Example:**
```
Resource: R0350 (Ben)
Total Weekly Capacity: 40 hours
Planned Project Work: 30 hours (week of 2026-02-15)
Absence: 2 days (16 hours) - VACATION - from 2026-02-15 to 2026-02-16

Available Capacity = 40 - 30 - 16 = -6 hours (OVER-ALLOCATED!)
```

When using `quantity` from absence records, always convert it into the **same unit** used by your capacity calculations (for example, convert days to hours if capacity is tracked in hours) so that Total Capacity, Planned Work, and Absence Quantity are consistent.

**Recommendation:** When assigning project work, always:
1. Query resource absences for the planned date range
2. Verify available capacity after subtracting absences
3. Reassign work to another resource if capacity is insufficient
4. Document the reassignment decision in project comments

### Best Practices

1. **Always Query Absences Before Assignment**
   - Check Employee Absences when proposing resource assignments to projects
   - Flag conflicts and suggest alternative resources
   - Absences are managed in Business Central's HR module, so check there for the most current information

2. **Batch Absence Queries**
   - Query absences for a date range rather than individual dates
   - Example: `fromDate le 2026-12-31 and toDate ge 2026-01-01`

3. **Monitor Capacity Throughout Project**
   - Absence data can change (new absences added, dates modified in HR module)
   - Re-validate resource assignments periodically
   - Update project plans if new absences conflict with allocations

4. **Coordinate with HR/Employee Management**
   - When absences conflict with project assignments, coordinate with HR
   - Request advance notice of planned absences to avoid resource conflicts
   - Communicate with project managers about resource unavailability

## Projects

### ⚠️ CRITICAL: Request Body Properties Only

**Use the below request body as template.** Only provide the properties listed. **Do not add any extra properties** unless explicitly specified by the user.

**NOTE:** Query parameters (like `select`, `filter`, etc.) are tool-level options and are separate from the request body. Only the JSON properties below should be included in the request.

### Required and Optional Properties

```json
{
  "number": "",
  "billToCustomerNo": "customer number",
  "description": "Project Description",
  "description2": "Additional Description (optional)",
  "startingDate": "2026-01-01",
  "endingDate": "2026-12-31"
}
```

**Legend:**
- `number`: Leave blank to auto-generate (if configured in Jobs Setup).
  - **ONLY** if you receive "cannot assign numbers automatically" error when trying to create the record, suggest a specific number to the user.
- `billToCustomerNo`: REQUIRED - Customer number from Business Central
- `description`: REQUIRED - Project name/description (max 100 characters)
- `description2`: OPTIONAL - Additional description field (max 50 characters)
- `startingDate`: REQUIRED - Project start date
- `endingDate`: REQUIRED - Project end date

### Example Usage

```json
{
  "number": "",
  "billToCustomerNo": "C00030",
  "description": "ACME Retail ERP Implementation",
  "startingDate": "2026-01-15",
  "endingDate": "2026-12-31"
}
```

**DO NOT include:**
- `status` (property not in template)
- `select` (this is a query parameter, not part of request body)
- `filter` (query parameter, not part of request body)
- Any other custom properties

# Modifying Records (PATCH instructions)

## Project Planning Lines - Resource Changes

### ⚠️ CRITICAL: Preserve Quantity When Changing Resources

**Business Central resets quantity to ZERO when changing the resource on a planning line.** To prevent data loss, you MUST follow this workflow:

**Workflow:**
1. **GET** the current planning line record
2. **Extract** the current `quantity` value from the response
3. **PATCH** with BOTH the new `resourceNo` AND the current `quantity`

### ✅ CORRECT - Always Include Current Quantity:
```json
{
  "If-Match": "*",
  "id": "systemid-from-get-request",
  "resourceNo": "NEW-RESOURCE-123",
  "quantity": 40
}
```
*Note: The `quantity` value (40) comes from the GET request in step 1*

### ❌ WRONG - Omitting Quantity:
```json
{
  "If-Match": "*",
  "id": "systemid",
  "resourceNo": "NEW-RESOURCE-123"
}
```
*This will reset quantity to ZERO - causing data loss!*

**Exception:** If the user explicitly requests to change BOTH resource and quantity, use the new quantity provided by the user instead of the current one.

## ⚠️ CRITICAL: Always Use Asterisk for If-Match

**DO NOT use `odata.etag` values.** When performing PATCH operations, ALWAYS use an asterisk (`*`) for the `If-Match` field. This applies to modifications done to **all entities**.

### ❌ WRONG - DO NOT USE:
```json
{
  "If-Match": "odata.etag",
  "id": "systemid",
  "number": "text",
  "unitPrice": 100,
  "lineAmount": 500
}
```

### ✅ CORRECT - ALWAYS USE:
```json
{
  "If-Match": "*",
  "id": "systemid",
  "number": "text",
  "unitPrice": 100,
  "lineAmount": 500
}
```

**Rules:**
- The `If-Match` field must ALWAYS have the value `"*"` (asterisk in quotes)
- NEVER use `"odata.etag"` or any other value
- This applies to ALL PATCH operations on ALL entities (Resources, Projects, Planning Lines, etc.)
- The asterisk bypasses concurrency checks and prevents update conflicts


## Reference Material 📚

- **Read** #file:.docs/Business_Central_Projects_Module_Study_Guide.md for understanding Business Central Projects module.
- **Fetch** [Project management](https://learn.microsoft.com/en-us/dynamics365/business-central/projects-manage-projects)
- **Fetch** [Walkthrough: managing projects](https://learn.microsoft.com/en-us/dynamics365/business-central/walkthrough-managing-projects-with-jobs)