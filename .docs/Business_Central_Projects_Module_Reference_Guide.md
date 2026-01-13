# Business Central Projects Module - Reference Guide

## Table of Contents
1. [Sources](#sources)
2. [Overview](#overview)
3. [Project Structure](#project-structure)
4. [Project Tasks](#project-tasks)
5. [Project Planning Lines](#project-planning-lines)
6. [Setting Up Projects](#setting-up-projects)
7. [Complete Project Lifecycle Example](#complete-project-lifecycle-example)
8. [Key Concepts and Best Practices](#key-concepts-and-best-practices)

---

## Sources

- [Project management](https://learn.microsoft.com/en-us/dynamics365/business-central/projects-manage-projects)
- [Walkthrough: managing projects](https://learn.microsoft.com/en-us/dynamics365/business-central/walkthrough-managing-projects-with-jobs)

## Overview

The Projects module in Business Central is designed to help organizations manage project accounting, track costs, schedule resources, and invoice customers. Projects enable you to:

- Schedule the usage of company resources
- Track various costs associated with resources on a specific project
- Monitor machine hours, employee hours, and material consumption
- Maintain budget information and monitor progress
- Invoice customers for completed work

### Why Use Projects in Business Central?

Projects provide a two-layer hierarchical structure that divides work into:
1. **Project Tasks** - the organizational and control layer
2. **Project Planning Lines** - the detailed execution and tracking layer

This structure enables better cost management, resource planning, progress tracking, and profitability analysis.

---

## Project Structure

Business Central uses a two-layer structure for projects:

### Layer 1: Project Tasks
- High-level organizational units within a project
- Every project must have at least one task
- Can be hierarchically organized (parent/child relationships)
- All posting to the project must reference a task
- Can have different billing customers and terms

### Layer 2: Project Planning Lines
- Detailed specifications of resource and material usage
- Define what will be invoiced to the customer
- Specify cost estimates and budgets
- Created within specific project tasks
- Include quantities, units, pricing, and scheduling information

**Why This Two-Layer Structure?**
- Enables division of projects into smaller, manageable pieces
- Allows specific details in budgeting, quotes, and registration
- Provides insight into project progress
- Tracks whether you're meeting milestones and budget expectations
- Supports multiple billing arrangements and cost centers

---

## Project Tasks

### Definition
Project tasks are the organizational units within a project. They represent distinct phases, deliverables, or work packages that make up the complete project.

### When to Use Project Tasks

**Use project tasks when you need to:**
- Break down complex projects into manageable phases
- Assign different costs, budgets, or billing customers to different parts of the project
- Group related planning lines together
- Create hierarchical structures (subtasks under main tasks)
- Generate reports and analyses by project phase
- Track progress at a granular level

### Project Task Types

Business Central offers three task types for hierarchical organization:

| Task Type | Purpose | Posting Allowed |
|-----------|---------|-----------------|
| **Begin-Total** | Marks the beginning of a summary group | No |
| **Posting** | Regular task where actual work occurs and posting happens | Yes |
| **End-Total** | Marks the end of a summary group | No |

### ⚠️ IMPORTANT: Job Task Numbering

**When creating Job Tasks from WBS-based systems (Microsoft Project, etc.):**
- ALWAYS use zero-padded task numbers to ensure correct sorting
- Follow the **Job Task Numbering Strategy** in [Business_Central_API_Guide.md](.docs/Business_Central_API_Guide.md#-critical-job-task-numbering-strategy)
- Example: WBS "1.10.3" must become Job Task No. "01.10.03" (not "1.10.3")
- Business Central sorts by ASCII text, NOT numerically

**For manually created tasks (as shown in examples below):**
- Use incremental numbering like 1000, 1010, 1020 (already sorts correctly)
- Or use zero-padded WBS-style numbering: 01, 01.01, 01.02

### Project Task Fields

| Field | Purpose |
|-------|---------
| **Project Task No.** | Unique identifier for the task within the project |
| **Description** | Descriptive name of the task |
| **Project Task Type** | Begin-Total, Posting, or End-Total |
| **Posting To** | The account to which costs are posted |
| **Bill-to Customer** | Customer to invoice for this task (if different from project customer) |
| **Ship-to Address** | Location where work is performed for this task |
| **Currency Code** | If task involves a different currency |
| **Budget (Total Cost)** | Estimated total cost for the task |
| **Usage (Total Cost)** | Actual costs incurred so far |

### Project Task Types Example: Consulting Project

```
1000 - Consulting on hall setup (Begin-Total)
  ├── 1010 - Consultation meeting with customer (Posting)
  ├── 1020 - Development (Posting)
  └── 1090 - Consulting Total (End-Total)
```

This structure shows parent-child relationships where the Begin-Total and End-Total items create a summary group, with individual Posting tasks for actual work.

---

## Project Planning Lines

### Definition
Project planning lines are detailed specifications that describe exactly what resources, items, and general ledger expenses will be consumed and/or invoiced for a project task.

### When to Use Project Planning Lines

**Use project planning lines when you need to:**
- Specify detailed resource allocations (hours by employee or resource)
- List specific items/materials needed for the task
- Define G/L account expenses
- Set up different billing arrangements (some costs invoiced, others not)
- Create budgets and compare actual vs. planned usage
- Reserve items for the project
- Support time tracking and expense reporting

### Planning Line Types

Each planning line has a type that determines its treatment in budgeting and invoicing:

| Line Type | Description | Budgeted | Invoiced | Use Case |
|-----------|-------------|----------|----------|----------|
| **Budget** | Estimated usage and costs | ✓ | ✗ | Time & materials projects where some costs aren't invoiced |
| **Billable** | Estimated invoicing to customer | ✗ | ✓ | Fixed price projects with defined deliverables |
| **Both Budget and Billable** | Same for budget and invoicing | ✓ | ✓ | Projects where you invoice what you use |

### Planning Line Content

Planning lines can specify the following types of resources:

#### 1. **Resource Planning Lines**
- Allocate specific employees or machines to tasks
- Examples: consultant hours, machine operation hours, specialized expertise
- Includes: resource code, hours needed, billing rate
- Used for: time-based billing, resource allocation, capacity planning

#### 2. **Item Planning Lines**
- Specify materials and inventory items needed
- Examples: software licenses, office supplies, construction materials
- Includes: item number, quantity, unit cost
- Used for: material budgeting, supply planning, cost estimation

#### 3. **G/L Account Planning Lines**
- Record anticipated general ledger expenses
- Examples: travel costs, subcontractor fees, consulting fees, overhead
- Includes: account number, description, amount, cost adjustment factors
- Used for: expense tracking, cost allocation, variance analysis

### Planning Line Fields

| Field | Purpose |
|-------|---------|
| **Line Type** | Budget, Billable, or Both Budget and Billable |
| **Planning Date** | When this work is scheduled to occur |
| **Type** | Resource, Item, or G/L Account |
| **No.** | The specific resource, item, or account being used |
| **Description** | Details about this line item |
| **Quantity** | How much of the resource/item is needed |
| **Unit Price** | The cost or billing rate per unit |
| **Total Price** | Calculated: Quantity × Unit Price |
| **Remaining Qty.** | Amount not yet transferred to the journal |

---

## Setting Up Projects

### Prerequisites for Project Management

Before creating projects, you must set up:

1. **Resources** - Define employees, contractors, and machinery available for projects
2. **Items** - Catalog materials and supplies that can be used on projects
3. **Time Sheets** (Optional) - If you want employees to report time usage
4. **Chart of Accounts** - General ledger accounts for posting project costs
5. **Project Posting Groups** - Links between projects and G/L accounts

### Step 1: Set Up Project Management (Global Settings)

Navigate to **Project Setup** to configure:

| Setting | Purpose |
|---------|---------|
| **Apply Usage Link by Default** | Automatically links project ledger entries to planning lines |
| **Default Task Billing Method** | Whether you bill one or multiple customers per project |
| **Automatic Update Project Item Cost** | Keeps item costs synchronized when inventory costs change |

### Step 2: Create a Project Card

The project card contains high-level information about the project:

**Essential Fields:**
- **Project No.** - Unique identifier
- **Description** - Project name and scope
- **Customer No.** - Bill-to customer (if not set per task)
- **Bill-to Customer** - Primary customer for invoicing
- **Project Manager** - Person responsible for the project
- **Starting Date** - Project start date
- **Ending Date** - Expected completion date
- **Status** - Active, Completed, On Hold, etc.
- **Description 2** - Additional project details

**Optional Fields:**
- **Responsible Employee** - For time sheet approvals
- **Location Code** - Default warehouse location
- **Currency Code** - If project uses non-base currency
- **Posting Group** - Which accounts to use for posting
- **Task Billing Method** - Single or multiple customer billing

### Step 3: Create Project Task Lines

For each project, create one or more tasks:

**Procedure:**
1. Open the project card
2. In the **Tasks** section, create lines with:
   - Task number (e.g., 1000, 1010, 1020)
   - Description of the task
   - Task type (Begin-Total, Posting, End-Total)
   - Optional: specific customer and billing terms

**Example Task Structure for an Office Build-Out Project:**

*Note: This example uses incremental numbering (1000, 1010, 1020) which sorts correctly. For WBS-based projects (from Microsoft Project), use zero-padded numbering (01, 01.01, 01.02) as documented in the Business_Central_API_Guide.md.*

```
Incremental Numbering (Manual Projects):
0000 - Project (Heading)
1000 - Planning & Design (Begin-Total)
  1010 - Space Assessment (Posting)
  1020 - Design Development (Posting)
1090 - Planning Total (End-Total)
2000 - Construction (Begin-Total)
  2010 - Demolition (Posting)
  2020 - Framing & Walls (Posting)
  2030 - Electrical Work (Posting)
2090 - Construction Total (End-Total)
3000 - Finishing (Posting)
4000 - Project Management & Overhead (Posting)
9999 - TOTAL (Total)

WBS-Style Numbering (From Microsoft Project):
00 - Project (Heading)
01 - Planning & Design (Begin-Total)
  01.01 - Space Assessment (Posting)
  01.02 - Design Development (Posting)
01.99 - Planning Total (End-Total)
02 - Construction (Begin-Total)
  02.01 - Demolition (Posting)
  02.02 - Framing & Walls (Posting)
  02.03 - Electrical Work (Posting)
02.99 - Construction Total (End-Total)
03 - Finishing (Posting)
04 - Project Management & Overhead (Posting)
99 - TOTAL (Total)
```

### Step 4: Create Project Planning Lines

For each posting task, create planning lines with the details:

**Procedure:**
1. Select a task with type "Posting"
2. Choose the **Project Planning Lines** action
3. Add lines specifying:
   - Line type (Budget, Billable, or Both)
   - Type of item (Resource, Item, or G/L Account)
   - Specific resource/item number
   - Quantity and dates
   - Pricing

**Example Planning Lines for Task 1010 (Space Assessment):**

| Type | No. | Description | Quantity | Unit | Unit Price | Line Type |
|------|-----|-------------|----------|------|-----------|-----------|
| Resource | 100 | Senior Consultant | 40 | hours | $150 | Both |
| Resource | 101 | Junior Consultant | 30 | hours | $75 | Both |
| Item | IT-SURVEY | Thermal Imaging Equipment | 1 | day | $500 | Both |
| G/L Account | 8200 | Travel Expenses | 1 | allowance | $1,000 | Both |

### Resource Scheduling Approaches

When planning resource allocation, you have three main approaches. Choose the one that best matches your project management and reporting needs:

#### Approach 1: Single Line with Total Quantity

Create one planning line with the total hours and a start date.

**Example:**
| Type | No. | Description | Qty | Unit | Planning Date | Line Type |
|------|-----|-------------|-----|------|-----------|-----------|
| Resource | SARAH | Senior Consultant | 40 | hrs | 1/15/2024 | Both |

**Advantages:**
- Simple to create and manage
- Fewer lines to maintain
- Good for high-level estimates
- Less administrative overhead

**Disadvantages:**
- End date is not explicit
- Harder to see specific daily allocation
- Less useful for detailed capacity planning
- Task shows only start date, not full duration

**Best for:**
- Quick project estimates
- Fixed-price projects with bulk allocations
- Simple projects with less detailed tracking needs

---

#### Approach 2: Multiple Lines by Time Period

Create separate planning lines for each time period (day, week, or milestone).

**Example:**
| Type | No. | Description | Qty | Unit | Planning Date | Line Type |
|------|-----|-------------|-----|------|-----------|-----------|
| Resource | SARAH | Senior Consultant | 8 | hrs | 1/15/2024 | Both |
| Resource | SARAH | Senior Consultant | 8 | hrs | 1/16/2024 | Both |
| Resource | SARAH | Senior Consultant | 8 | hrs | 1/17/2024 | Both |
| Resource | SARAH | Senior Consultant | 8 | hrs | 1/18/2024 | Both |
| Resource | SARAH | Senior Consultant | 8 | hrs | 1/19/2024 | Both |

**Advantages:**
- Clear visibility of daily/period allocation
- Task shows actual start and end dates
- Better for capacity planning across projects
- Easier to adjust specific periods
- Granular budget tracking by date

**Disadvantages:**
- More planning lines to create and maintain
- More effort to update if schedule changes
- More complex initial setup
- Can create visual clutter in planning lines

**Best for:**
- Complex resource scheduling
- Projects requiring detailed capacity planning
- When daily/weekly allocation visibility is critical
- Multi-project resource management

---

#### Approach 3: Mix & Match (Hybrid)

Use a combination of summary lines and detail lines based on planning importance.

**Example:**
| Type | No. | Description | Qty | Unit | Planning Date | Line Type |
|------|-----|-------------|-----|------|-----------|-----------|
| Resource | SARAH | Assessment Phase Summary | 40 | hrs | 1/15/2024 | Budget |
| Resource | SARAH | Initial Assessment Meeting | 8 | hrs | 1/15/2024 | Both |
| Resource | SARAH | Document Review | 16 | hrs | 1/16/2024 | Both |
| Resource | SARAH | Final Assessment Report | 16 | hrs | 1/19/2024 | Both |

**Advantages:**
- Balance between detail and simplicity
- Track key milestones explicitly
- Easier rescheduling of bulk items
- Combines budget tracking with detailed work phases
- Flexible to project needs

**Disadvantages:**
- Requires judgment about what to detail
- Potential for duplicated budget vs. detail lines
- More complex to understand at a glance
- Can lead to inconsistent planning approaches

**Best for:**
- Projects with both fixed phases and flexible work
- Mix of billable and internal work
- Organizations transitioning from simple to detailed planning
- Projects with significant milestones

---

#### How to Choose Your Approach

| Factor | Single Line | Multiple Lines | Mix & Match |
|--------|-----------|-----------------|-----------|
| Project Complexity | Simple | Complex | Medium |
| Resource Scheduling Detail | Low | High | Medium |
| Capacity Planning Needs | Low | High | Medium |
| Daily Tracking Required | No | Yes | Selective |
| Invoicing Frequency | Bulk | Daily/Period | Milestone |
| Administrative Effort | Low | High | Medium |
| Best for Fixed Price | Yes | No | Mixed |
| Best for Time & Materials | Moderate | Yes | Yes |

---

#### Converting Between Approaches

You can change your approach mid-project if needed:

1. **Single to Multiple:** Create additional lines with different dates for the remaining work
2. **Multiple to Single:** Consolidate remaining lines into a summary line
3. **To Hybrid:** Create summary lines for completed work, detail lines for remaining work

### Step 5: Set Up Project-Specific Pricing (Optional)

You can override standard prices for specific projects:

**Navigate to:**
- **Project Resource Prices** - for resource billing rates by task
- **Project Item Prices** - for material costs by task
- **Project G/L Account Prices** - for expense amounts by task

This allows you to:
- Charge different rates for the same resource on different projects
- Apply cost adjustment factors (markup for materials, etc.)
- Set project-specific discounts

---

## Complete Project Lifecycle Example

### The Scenario
**ACME Consulting** is hired by **Contoso Manufacturing** to upgrade their conference facilities. The project includes:
- Feasibility assessment (2 weeks)
- Design development (4 weeks)
- Installation and setup (3 weeks)
- Training and handover (1 week)

**Budget:** $75,000  
**Duration:** 10 weeks  
**Team:** 2 senior consultants, 1 installer, 1 trainer

---

### Phase 1: Initial Planning & Setup (Week 0)

#### 1.1 Create the Project Card

**Navigate to:** Projects → New

| Field | Value |
|-------|-------|
| Project No. | CONF-2024-001 |
| Description | Contoso Conference Room Upgrade |
| Customer No. | CONTOSO |
| Bill-to Customer | Contoso Manufacturing |
| Project Manager | Sarah Johnson |
| Starting Date | 1/15/2024 |
| Ending Date | 3/31/2024 |
| Posting Group | CONSULTING |
| Status | Planning |

#### 1.2 Create Project Task Structure

On the project card, create these tasks:

*Note: Using incremental numbering for this manually-created project. For Microsoft Project imports, use zero-padded WBS numbering (see Business_Central_API_Guide.md).*

```
ASSESSMENT
100 - Assessment Phase (Begin-Total)
  110 - Facility Assessment (Posting)
  120 - Requirements Analysis (Posting)
  190 - Assessment Total (End-Total)

DESIGN
200 - Design Phase (Begin-Total)
  210 - Concept Design (Posting)
  220 - Technical Design (Posting)
  230 - Customer Review (Posting)
  290 - Design Total (End-Total)

IMPLEMENTATION
300 - Implementation Phase (Begin-Total)
  310 - Equipment Installation (Posting)
  320 - Network Integration (Posting)
  330 - System Testing (Posting)
  290 - Implementation Total (End-Total)

CLOSEOUT
400 - Training & Handover (Posting)
```

#### 1.3 Create Planning Lines for Assessment Phase

**For Task 110 - Facility Assessment:**

| Type | No. | Description | Qty | Unit | Unit Price | Line Type | Planning Date |
|------|-----|-------------|-----|------|-----------|-----------|-----------|
| Resource | SARAH | Senior Consultant | 40 | hrs | $150 | Both | 1/15/2024 |
| Resource | MIKE | Consultant | 20 | hrs | $100 | Both | 1/15/2024 |
| Item | EQUIP-THERMAL | Thermal Camera | 1 | day | $500 | Budget | 1/16/2024 |
| G/L Account | 6100 | Travel & Meals | 1 | allow | $800 | Both | 1/15/2024 |

*Note: Planning Date is the scheduled date for the work. The quantities represent total hours/items needed. Actual daily hours are recorded later via time sheets or project journals.*

**For Task 120 - Requirements Analysis:**

| Type | No. | Description | Qty | Unit | Unit Price | Line Type | Planning Date |
|------|-----|-------------|-----|------|-----------|-----------|-----------|
| Resource | SARAH | Senior Consultant | 30 | hrs | $150 | Both | 1/22/2024 |
| Resource | MIKE | Consultant | 30 | hrs | $100 | Both | 1/22/2024 |
| Item | SOFT-VISIO | Visio License | 1 | month | $200 | Budget | 1/22/2024 |

---

### Phase 2: Execution (Weeks 1-10)

#### 2.1 Record Time Usage

As work progresses, the project team records actual hours via:
- **Time Sheets** - Employees enter their hours
- **Project Journals** - Post actual costs and usage

**Example Journal Entry - Assessment Complete:**

| Date | Project | Task | Type | No. | Description | Qty | Amount |
|------|---------|------|------|-----|-------------|-----|--------|
| 1/19/2024 | CONF-2024-001 | 110 | Resource | SARAH | Facility Assessment | 42 | $6,300 |
| 1/19/2024 | CONF-2024-001 | 110 | Resource | MIKE | Facility Assessment | 22 | $2,200 |
| 1/19/2024 | CONF-2024-001 | 110 | Item | EQUIP-THERMAL | Equipment Rental | 1 | $500 |
| 1/19/2024 | CONF-2024-001 | 110 | G/L Acct | 6100 | Travel & Meals | 1 | $850 |

#### 2.2 Monitor Progress

During execution, project managers:
- Compare actual usage vs. planning lines (Budget vs. Actual)
- Review the **Remaining Quantity** on planning lines
- Monitor budget consumption
- Track profitability

**Progress Check - After Assessment Phase:**

| Task | Budget Cost | Actual Cost | Variance | % Complete |
|------|------------|------------|----------|-----------|
| 110 - Assessment | $9,200 | $9,850 | -$650 (7% over) | 100% |
| 120 - Requirements | $8,000 | $7,500 | +$500 (6% under) | 100% |
| Design Phase | $15,000 | $0 | — | 0% |
| Implementation | $35,000 | $0 | — | 0% |
| Training | $7,800 | $0 | — | 0% |
| **TOTAL** | **$75,000** | **$17,350** | **+$57,650** | **23%** |

#### 2.3 Manage Changes

If scope changes (e.g., customer requests additional features):
1. Modify planning lines - increase quantities or add new lines
2. Add new planning lines with additional costs
3. Adjust task budgets as needed
4. Document change requests and approvals

**Example Change Request - Additional Camera Installation:**

- **Original Task 320:** Network Integration (40 hours, $4,000)
- **Change:** Add security camera installation (new line)
- **Action:** Add planning line to Task 320:
  - Type: G/L Account
  - Description: Security Camera Installation (Subcontractor)
  - Amount: $3,500
  - Line Type: Both Budget and Billable

---

### Phase 3: Invoicing

#### 3.1 Create Progress Invoices

As work is completed, create invoices for billable items:

**Navigate to:** Project Task Lines → Create Sales Invoice

**Invoice 1 - Assessment Phase Completion (1/26/2024):**
- Only task lines with type "Posting" are invoiced
- Only items marked "Billable" or "Both" are included
- Contoso receives invoice for:
  - Assessment work: $9,850 (actual cost + markup)
  - Analysis work: $7,500
  - **Invoice Total: $17,350**

**Invoice 2 - Design Phase Completion (2/23/2024):**
- Design task invoices: $16,200
- **Running Total: $33,550**

#### 3.2 Invoice By Task

You can create invoices by task to match project schedule:
- Down payment (if agreed)
- Milestone completion
- Monthly progress billing
- Final invoice upon completion

---

### Phase 4: Closing the Project

#### 4.1 Final Posting and Reconciliation

Once all work is complete:

1. **Post Final Usage** - Record any remaining time, materials, and expenses
2. **Review WIP** (Work in Process) - Verify all costs are captured
3. **Reconcile Budget vs. Actual** - Final cost analysis

**Final Project Reconciliation:**

| Item | Budget | Actual | Variance |
|------|--------|--------|----------|
| **Assessment Phase** | $17,200 | $17,350 | -$150 |
| **Design Phase** | $15,000 | $15,200 | -$200 |
| **Implementation Phase** | $35,000 | $33,900 | +$1,100 |
| **Training & Handover** | $7,800 | $7,650 | +$150 |
| **TOTAL** | **$75,000** | **$74,100** | **+$900 (1.2% under budget)** |

#### 4.2 Create Final Invoice

If not already invoiced (fixed-price project), create the final invoice:
- Include all billable planning lines
- Deduct advance payments if applicable
- Include any change orders

**Final Invoice:**
- Design & Assessment: $32,550 (prior invoices)
- Implementation & Training: $41,550
- **Total Project Invoice: $74,100**

#### 4.3 Close the Project

Change project status from "Active" to "Completed":

1. Navigate to the project card
2. Set **Status** = "Completed"
3. Update **Ending Date** if different from planned
4. Add completion notes and lessons learned

**Project Closure Checklist:**
- [ ] All work completed and approved by customer
- [ ] All time sheets and expenses posted
- [ ] All invoices issued and paid
- [ ] Budget vs. actual reconciled
- [ ] Project marked as Completed
- [ ] Lessons learned documented
- [ ] Project data archived

#### 4.4 Project Profitability Analysis

**Final Analysis - CONF-2024-001:**

| Category | Amount |
|----------|--------|
| Total Revenue | $74,100 |
| Total Costs | $74,100 |
| **Gross Profit** | **$0** |
| Profit Margin | 0% |

*Note: This project was break-even. With markup applied during invoicing, gross profit would be positive.*

---

## Key Concepts and Best Practices

### Concept 1: Planning Lines Determine Invoicing

**Critical Understanding:**
- Planning lines are the **only way** to define what gets invoiced to the customer
- Simply posting costs to a project task does **NOT** automatically invoice them
- You must explicitly create planning lines with "Billable" or "Both" type to invoice

**Best Practice:**
- Create planning lines at the start of the project to establish what will be billed
- Keep planning lines updated as scope changes
- Review planning lines before each invoice run

### Concept 2: Budget vs. Billable vs. Both

**Decision Guide:**

Use **Budget** when:
- Internal costs that should not be invoiced to customer
- Overhead allocation to the project
- Tracking internal resource costs separate from billing

Use **Billable** when:
- Fixed-price items that are invoiced regardless of actual cost
- Items where the cost isn't relevant to billing (e.g., "design work: $5,000 flat fee")

Use **Both Budget and Billable** when:
- Time & materials projects where you invoice what you use
- Standard consulting arrangements where you bill for actual hours

### Concept 3: Task Hierarchy and Reporting

**Benefits of Hierarchical Tasks:**

- **Begin-Total/End-Total** tasks create summary rows
- Parent tasks show total costs of all child tasks
- Reports can be generated by task level
- Easier to identify cost overruns in specific phases

**Example Hierarchy Report:**
```
CONF-2024-001 Assessment Phase (Begin-Total)  $17,350
  └─ Assessment Task                           $9,850
  └─ Analysis Task                             $7,500
```

### Concept 4: Usage Tracking with "Apply Usage Link"

**What It Does:**
- Links planning lines to actual posted usage
- Tracks "Remaining Quantity" on planning lines
- Prevents over-invoicing of fixed items
- Enables "Both Budget and Billable" functionality

**When to Enable:**
- Time & materials projects
- Projects where you need to prevent over-charging
- Projects with resource availability constraints

### Concept 5: Project-Specific Pricing

**Why Use It:**
- Different resources have different rates for different projects
- Special customer discounts or rates
- Cost adjustment factors (material markup, overhead allocation)

**Example:**
- Standard resource rate: $100/hour
- Project CONF-2024-001 rate: $150/hour (premium project)
- Project SPECIAL-01 rate: $80/hour (strategic account discount)

### Best Practice Checklist

#### Before Starting a Project
- [ ] Project card created with accurate dates and customer
- [ ] All tasks defined with clear descriptions
- [ ] Task hierarchy established (Begin-Total/End-Total groups)
- [ ] All planning lines created for initial scope
- [ ] Budgets established and approved
- [ ] Project-specific pricing configured if needed
- [ ] Team members assigned and notified
- [ ] Posting group assigned (for G/L account mapping)

#### During Project Execution
- [ ] Time/expenses posted regularly (weekly or bi-weekly)
- [ ] Progress invoices created on schedule
- [ ] Budget vs. actual monitored monthly
- [ ] Change requests documented and planned lines updated
- [ ] Team utilization reviewed
- [ ] Scope creep prevented through formal change control

#### At Project Completion
- [ ] All work posted and costs recorded
- [ ] Final invoice created and sent
- [ ] Budget reconciliation completed
- [ ] Profitability analysis performed
- [ ] Lessons learned documented
- [ ] Project marked as Completed
- [ ] Files and documentation archived

---

## Common Project Scenarios

### Scenario 1: Time & Materials Project

**Characteristics:**
- Customer pays for actual hours and materials used
- Difficult to estimate final cost upfront
- Need flexibility in scope

**Setup:**
- Planning lines: Use "Both Budget and Billable"
- Enable "Apply Usage Link"
- Create planning lines with estimated quantities
- Invoice actual usage monthly or upon completion
- Budget lines help track variance from estimates

### Scenario 2: Fixed-Price Project

**Characteristics:**
- Customer pays a fixed amount regardless of actual costs
- Need tight cost control
- Scope is well-defined

**Setup:**
- Planning lines: Mix "Budget" (track actual) and "Billable" (invoice)
- Detailed cost estimates in budget lines
- Regular monitoring of actual vs. budget
- Invoice the fixed amount (from billable lines) when complete
- Any profit/loss is realized upon invoicing

### Scenario 3: Multi-Customer Project

**Characteristics:**
- Work performed for multiple customers
- Different customers pay for different portions
- Need to allocate costs by customer

**Setup:**
- Set "Task Billing Method" = "Multiple"
- On each task, specify which customer to bill
- Create separate planning lines per customer
- Generate invoices per customer
- Each customer only sees their portion

### Scenario 4: Project with Retainer/Down Payment

**Characteristics:**
- Customer pays upfront or in installments
- Need to track payment schedule
- Expenses incurred separately from invoicing

**Setup:**
- Create tasks for each payment milestone
- Task 1000: Down Payment (Invoice only, no budget)
- Task 2000: Usage (Budget and Billable, actual work)
- Task 3000: Final Payment (Invoice only)
- Invoice payment tasks separately from work tasks

---

## Summary

### When to Use Project Tasks vs. Planning Lines

| Need | Use | Why |
|------|-----|-----|
| Organize project into phases | Project Tasks | Organizational hierarchy, reporting |
| Specify what will be billed | Planning Lines | Invoicing is based on planning lines |
| Allocate resources | Planning Lines | Resource-specific tracking |
| Track budgets | Planning Lines | Budget amounts set at line level |
| Assign different customers | Project Tasks | Can specify customer per task |
| Monitor progress | Project Tasks | Tasks provide phase-level overview |
| Control scope | Planning Lines | Lines define scope limits |

### Key Takeaways

1. **Two-Layer Structure:** Projects require both tasks and planning lines
2. **Planning Lines Define Invoicing:** What you invoice comes from planning lines, not from posted costs
3. **Budget vs. Billable:** Choose the right line type based on whether you're budgeting, invoicing, or both
4. **Hierarchy Matters:** Use Begin-Total/End-Total to organize related tasks
5. **Ongoing Monitoring:** Track budget vs. actual throughout project execution
6. **Formal Closure:** Close projects properly to complete the accounting cycle

---

## Additional Resources

- Microsoft Learn: Create projects in Business Central
- Microsoft Learn: Set up project planning lines
- Microsoft Learn: Set up project task lines
- Microsoft Learn: Walkthrough - Managing projects
- Official documentation: Project Management in Business Central

