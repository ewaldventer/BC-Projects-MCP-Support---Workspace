# Generic project and resource management prompts

> **NOTE**: These are some of the prompts that I have personally used during testing and development

1. Create a new project in Business Central based on #filename
2. I have uploaded a new file in Business Central named #filename. Please find the most recently uploaded files, confirm with me which one to use and then create a new project using the data in this file.
3. Use the split line function to split the project planning lines
4. List configuration templates
5. Read #file:Business_Central_API_Guide.md . Create a resource based on the resource placeholder template, and change the Description to "Senior Developer" and set the unit cost to 1500, and unit price to 2000. 
6. Add the following resource skills to the resource Ben:
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
7. Review project planning lines for project PR00170, and find the best candidate resource to assign to each project line based on the required skills. Propose the assignments before making the actual change.
8. Please check if there are any leave of absence that would impact resource availability for the next 3 weeks. If there are, advise on potential alternative resources.
9. #fetch https://date.nager.at/Api, review the api documentation and fetch the public holidays for 2026 in South Africa?
10. Fetch project document 10, read the document lines and advise if it contains adequate information to create a project plan with WBS structure and tasks.


# Project & Resource Ledger Reporting Prompts

> **NOTE**: The following prompts have been suggested by Copilot based on the context of project and resource management in Business Central. 

These prompts are designed to work with the project ledger data to analyze project performance, resource utilization, and profitability. Use `[PROJECT_NO]` and `[RESOURCE_NO]` as placeholders and replace with actual values.

## Project Performance & Cost Analysis

1. Analyze the project ledger for [PROJECT_NO] and compare actual costs against planned budget. Identify any task areas where costs are exceeding estimates.
2. Generate a cost summary by task for project [PROJECT_NO]. Show planned hours vs actual hours worked, and cost variance.
3. Which project has the highest cost overrun so far? Break down the overrun by task and resource.
4. Show the top 5 most expensive resources (by total cost) used across all projects in the last 30 days.
5. For project [PROJECT_NO], calculate the revenue vs cost ratio by task. Which tasks are most profitable?

## Resource Utilization & Performance

6. List all billable and non-billable hours recorded by resource [RESOURCE_NO] in the last month. Calculate total revenue generated, total cost incurred, and billability ratio (billable % vs non-billable %).
7. Which resources are underutilized in project [PROJECT_NO]? Are there resources with no ledger entries despite planning assignments?
8. Generate a resource performance report showing: total hours worked, billable hours, non-billable hours, cost per hour, billability %, and utilization rate for [PROJECT_NO].
9. Identify resources that are overallocated (actual hours exceed planned hours) in project [PROJECT_NO]. Break down the overallocation by billable vs non-billable work.
10. Cross-reference project planning lines with project ledger entries for [PROJECT_NO]. Which planned tasks have NOT been started yet?

## Planning vs Actual Analysis

11. For project [PROJECT_NO], create a variance report: compare each planning line's planned hours against actual hours recorded in the ledger.
12. Show all project ledger entries for [PROJECT_NO] that have $0 revenue. Investigate whether these are rework, fixes, or data entry errors.
13. Identify projects where actual work has started but planning lines haven't been created. Are there missing planning records?

## Resource Availability & Conflict Detection

14. Before assigning resource [RESOURCE_NO] to project [PROJECT_NO], check: (a) employee absences during project date range, (b) current workload in other projects, (c) skill match with required tasks.
15. For each resource assigned to project [PROJECT_NO], calculate available capacity after subtracting absences and other project commitments. Flag any over-allocation.
16. Which resources will have availability conflicts (absences or other projects) during the critical path of [PROJECT_NO]?

## Task & Milestone Tracking

17. Show the timeline of ledger entries for project [PROJECT_NO] by task. Which tasks are on schedule, behind, or ahead of plan?
18. For project [PROJECT_NO], identify which task areas have no ledger entries yet. When are they scheduled to start?
19. Generate a milestone completion report for [PROJECT_NO] showing: % complete by task, cost-to-date, and estimated cost to complete.

## Profitability & Billing

20. Which project is generating the highest profit margin? Show revenue vs cost for each project, broken down by billable and non-billable hours.
21. Identify projects or tasks where billable hours significantly differ from cost hours (indicating pricing issues or rework). Show non-billable hours as a % of total hours.
22. For resource [RESOURCE_NO], calculate the average billing rate across all projects. Is it consistent? Also show non-billable hours trends.
23. Show all ledger entries with negative or zero revenue. Investigate whether these are rework, fixes, or data entry errors. Calculate total non-billable hours and cost impact.

## Risk & Trend Analysis

24. If current burn rate continues, what is the projected final cost for project [PROJECT_NO]? Will it exceed budget? Include non-billable hours in the forecast.
25. Identify resources with the highest error/rework rate (repeated entries on same task with varying costs). Show non-billable hours as a proxy for rework.
26. For project [PROJECT_NO], is work progressing faster or slower than planned? Show days ahead/behind by task. Compare billable vs non-billable progress.
27. Forecast resource demand for project [PROJECT_NO] for the next 4 weeks based on remaining planning lines. Estimate billable vs non-billable allocation.
28. Compare actual resource costs to planned resource costs for [PROJECT_NO]. Which resource cost the most to use vs plan? Include non-billable cost impact.

## All-Resources Summaries & Non-Billable Tracking

29. Generate a cross-project resource summary: for each resource, show total hours worked, billable hours, non-billable hours, non-billable %, and revenue. Identify top non-billable resource contributors.
30. Which resources have the highest non-billable ratio? List resources by non-billable percentage and absolute non-billable hours. Investigate root causes.
31. Compare billability metrics across all projects: total hours, billable hours, non-billable hours, non-billable %. Which project has the highest non-billable ratio?
32. Generate a non-billable hours trend report: show weekly or monthly non-billable hours across all resources and projects. Are non-billable hours increasing or decreasing?
33. For all projects combined, calculate: total billable revenue, total cost (billable + non-billable), and profit margin. Show billable vs non-billable cost breakdown.
34. Create a resource utilization dashboard: for each resource, show utilization %, billable %, non-billable %, and average cost per billable hour. Identify outliers.
35. Show all non-billable hours recorded across all projects with reasons (rework, training, overhead, etc.). Recommend actions to reduce non-billable time.
36. Generate a "billability scorecard" for each resource: hours billed, revenue generated, non-billable ratio, and efficiency rating (billable revenue / total cost). Rank resources by efficiency.

## Example Usage

**From PR00170 Ledger Data:**
- "Analyze the project ledger for PR00170 and compare actual costs against planned budget. Identify any task areas where costs are exceeding estimates."
- "List all billable and non-billable hours recorded by resource R0350 in the last month. Calculate total revenue generated, total cost incurred, and billability ratio."
- "Show all project ledger entries for PR00170 that have $0 revenue. Investigate whether these are rework, fixes, or data entry errors. Calculate total non-billable hours and cost impact."
- "Generate a cross-project resource summary: for each resource, show total hours worked, billable hours, non-billable hours, non-billable %, and revenue."
- "Which resources have the highest non-billable ratio? List resources by non-billable percentage and absolute non-billable hours. Investigate root causes."
