---
name: spn-demo-creator
description: "Create Snowflake CoWork demos with synthetic data, a semantic view, a Cortex Search service, a Cortex Agent and a ready-to-present demo script, for a named customer or for a whole industry. Use this skill whenever the user wants to build, create or prepare a demo, a POC story, a talk track or a use case for Snowflake CoWork, Snowflake Intelligence or Cortex Agents, even if they only name a company or industry and do not say CoWork. Triggers: CoWork demo, Snowflake Intelligence demo, create demo, build demo, customer demo, industry demo, partner demo, demo script, demo for."
---

# CoWork Demo Creator

Create an end-to-end Snowflake CoWork demo: realistic synthetic data, a semantic view, a Cortex Search service, a Cortex Agent and a timed demo script. Works in two modes.

| Mode | Use when | Research |
|---|---|---|
| **Industry** | No named customer yet, or a workshop or lunch and learn | Industry trends, common data pain points, typical personas |
| **Customer** | A named prospect or customer | Company website, public news, optional audience research |

## Where files are

`references/` and `sql/` are folders next to this SKILL.md. Resolve them against this skill's own folder, not the current project, because the skill may be installed from a cache and the project folder will not contain them.

## Prerequisites

Read `references/prerequisites.md`. In short, you need:
- A Snowflake account where you can create a database, schema, warehouse, Cortex Search service and Cortex Agent
- Cross-region inference enabled if your region lacks the orchestration model (needs ACCOUNTADMIN)
- Web search enabled for agents in Snowsight (AI & ML > Agents > Settings)
- Optional: the email tool. Run `sql/00_setup.sql` once as ACCOUNTADMIN

Use the demo database and warehouse names the user gives you. Defaults: database `COWORK_DEMOS`, warehouse `COWORK_DEMO_WH`. Never assume a specific account or connection name. Ask which connection to use and confirm it before creating anything.

## Workflow

Present the plan (Steps 1 to 3, then a summary of Steps 4 to 11) and get the user's approval before building anything. If the user is in a planning mode, stay in it until they approve. Otherwise, ask for approval in chat.

### Step 0: Choose the mode

Ask: "Is this demo for a named customer, or for an industry?" Then follow Step 1A or 1B.

### Step 1A: Industry mode

**Goal:** Understand the industry well enough to propose realistic use cases.

1. If the user has not named an industry, ask for one, and the country or region if it matters.
2. Research with web search:
   - Industry size, structure and key trends in the region
   - Typical data-intensive processes, and the metrics leaders track
   - Common pain points: fragmented data, manual reporting, slow root-cause analysis
   - Typical personas (for example COO, head of operations, analyst)
   - Relevant unstructured sources: customer feedback, incident reports, regulatory notes, market reports
3. Summarize findings and ask the user to confirm or correct them.

**Output:** Industry context summary

### Step 1B: Customer mode

1. If the user has not given the customer's website, STOP and ask for it.
2. Research with web search: company overview, products and services, geography, recent news, competitors, data-intensive processes.
3. Summarize findings for confirmation.

**Output:** Customer context summary

### Step 2: Audience (optional)

Ask whether the user wants to tailor the demo to specific attendees.

- **Industry mode:** describe the roles in the room (for example "partner SEs and solution architects"). Do not research named individuals.
- **Customer mode:** only research people if the user provides them. Use only public professional information. Do not infer anything sensitive about a person. If the user prefers, describe the roles instead.

Present a short audience brief: roles, priorities, likely pain points, preferred level of detail, what to avoid. ⚠️ **STOP for approval.**

### Step 3: Define the use case

1. If the user has a use case, use it. Otherwise ask whether they want to supply one or have you suggest three.
2. If suggesting, present three options. For each:
   - Title and the business problem
   - Structured data angle (metrics, KPIs, trends)
   - Unstructured data angle (documents, feedback, reports)
   - Why it resonates with this industry or customer, and with this audience
   - Target personas
3. The chosen use case must cover both structured and unstructured data.
4. Present a summary for confirmation:
   - Selected use case and business problem
   - Structured questions (Cortex Analyst), unstructured questions (Cortex Search), combined questions
   - Target personas

⚠️ **STOP for approval before building.**

### Step 4: Create the schema

1. Sanitize the name (uppercase, underscores, no special characters). Use the customer name, or the industry and use case in industry mode.
2. Check whether the schema exists. If so, ask the user for another name.
3. Run:
   ```sql
   CREATE DATABASE IF NOT EXISTS COWORK_DEMOS;
   CREATE SCHEMA IF NOT EXISTS COWORK_DEMOS.<DEMO_NAME>;
   USE SCHEMA COWORK_DEMOS.<DEMO_NAME>;
   ```
   Use the user's database name if they gave one. Run `USE DATABASE` before `CREATE SCHEMA`; right after `CREATE DATABASE` the schema statement can race.
4. Write a short `plan.md` in the user's project folder with the mode, use case, names and the story (the three acts from Step 5).

### Step 5: Design the data model

Load `references/data-templates.md`.

- First write the **story contract**: three acts the data must support.
  1. *What:* a headline number with a clear split (for example completed vs lost).
  2. *Rule out the obvious:* a dimension the audience will suspect (department, region, staffing) that has NO real effect. Plant near-equal rates across it.
  3. *Find the real drivers:* two or three hidden drivers (for example customer age group, day and time slot) with big, measurable effects.
  Write each driver with target rates, such as "segment A ~45%, segment B ~12%, baseline ~20%". These numbers are the contract for Step 6.
- 4 to 6 structured tables with domain terminology, time series and dimensions. Add pre-computed helper columns (an age group, a day of week) so the agent can find the drivers easily.
- 1 or 2 unstructured tables (feedback, reports, incident notes) with a text column for search and filterable metadata (date, category, sentiment, source).
- Present table names, columns and types. ⚠️ **STOP for approval.**

### Step 6: Generate synthetic data

- In customer mode, use the customer's real product names and regions. In industry mode, use realistic but fictional organizations. Never use real personal data.
- 200 to 1000 rows per structured table and 50 to 250 documents. Use realistic distributions, not uniform random. Dates in the last 12 to 24 months.
- Plant the story contract from Step 5. Do NOT use plain random values for the driver columns. Use one random roll per row and thresholds per segment (see `references/data-templates.md`) so the target rates hold.
- Create tables one at a time, in dependency order (dimensions first). Load dimensions in parallel, then load the fact tables in order, because facts depend on dimension helper columns.
- Execute the CREATE TABLE and INSERT statements, then verify row counts.
- **Verify the patterns.** Run one GROUP BY query per act (overall split, the ruled-out dimension, each driver) and compare to the target rates. If they are significantly off, adjust the thresholds and reload. ⚠️ **STOP and show the user the verification results.**

### Step 7: Create the semantic view

Use the `semantic-view` skill if available. Rules that commonly trip people up:

- Metrics use an aggregation in `expr` (for example `SUM(col)`) and have no `data_type`.
- Dimensions, time dimensions and facts must have `data_type`.
- Relationships use `relationship_columns`. Referenced tables must have a `primary_key`.
- Include 8 verified queries (the 6 demo questions plus 2 extras) and one business metric that shows a KPI calculation.

Create the view from the YAML, either inline or from a stage. Inline (as used in other proven demo skills, where the third argument `FALSE` means create rather than verify only):
```sql
CALL SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML('COWORK_DEMOS.<DEMO_NAME>', $$<YAML>$$, FALSE);
```
From a stage:
```sql
CREATE STAGE IF NOT EXISTS COWORK_DEMOS.<DEMO_NAME>.SEMANTIC_MODELS;
-- upload the YAML with PUT or snow stage copy
CREATE OR REPLACE FILE FORMAT COWORK_DEMOS.<DEMO_NAME>.YAML_FF
  TYPE = CSV FIELD_DELIMITER = NONE RECORD_DELIMITER = NONE;
CALL SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML(
  'COWORK_DEMOS.<DEMO_NAME>',
  (SELECT $1 FROM @COWORK_DEMOS.<DEMO_NAME>.SEMANTIC_MODELS/<file>.yaml
    (FILE_FORMAT => 'COWORK_DEMOS.<DEMO_NAME>.YAML_FF')));
```
Verify with `SHOW SEMANTIC VIEWS IN SCHEMA ...` and test with Cortex Analyst. Check the current Snowflake documentation if a statement fails, because syntax changes.

### Step 8: Create the Cortex Search service

```sql
CREATE OR REPLACE CORTEX SEARCH SERVICE COWORK_DEMOS.<DEMO_NAME>.<SEARCH_NAME>
  ON <text_column>
  ATTRIBUTES <filterable_columns>
  WAREHOUSE = COWORK_DEMO_WH
  TARGET_LAG = '1 hour'
  AS (SELECT * FROM <unstructured_table>);
SHOW CORTEX SEARCH SERVICES IN SCHEMA COWORK_DEMOS.<DEMO_NAME>;
```
Wait 2 to 3 minutes for indexing. The service must be ACTIVE.

### Step 9: Create the Cortex Agent

1. You must have a one or two sentence agent description. Ask if missing. It becomes the agent `COMMENT`.
2. Create the agent with `CREATE OR REPLACE AGENT ... COMMENT = '...' FROM SPECIFICATION $$...$$`. Start from `references/agent-spec-template.yaml`.
3. Tools: Cortex Analyst (`cortex_analyst_text_to_sql`), Cortex Search (`cortex_search`), `web_search`, `data_to_chart`, and optionally `send_email` (a `generic` tool that calls `COWORK_DEMOS.PUBLIC.SEND_MAIL`, created by `sql/00_setup.sql`).
4. Custom tools must use type `generic`, and their `tool_resources` need `type`, `execution_environment` and `identifier`.
5. Include at least 5 `sample_questions` that follow the demo story (what, what, why, why, how).
6. Create the agent in the demo schema. If it does not appear in Snowflake's agent list for your account, create it in the schema your account lists agents from (some accounts use `SNOWFLAKE_INTELLIGENCE.AGENTS`) and grant USAGE on that database and schema.
6. Validate with `SHOW AGENTS IN SCHEMA ...` and a test question. If the agent fails, check that the semantic view and the search service are both active, and that the role has USAGE on the agent.

### Step 10: Sample questions

Write 6 questions that build on each other:

| # | Type | Level | Purpose |
|---|---|---|---|
| 1 | What | Descriptive | Metric or fact |
| 2 | What | Descriptive | Trend or comparison |
| 3 | Why | Diagnostic | Rule out the obvious cause (the dimension with no effect) |
| 4 | Why | Diagnostic | Find the real drivers, with qualitative evidence from search |
| 5 | How | Prescriptive | Recommendation with evidence |
| 6 | How | Prescriptive | Action plan, and the email if enabled |

If email is not set up, question 6 ends with the action plan instead. Present all six with expected answer summaries.

### Step 10b: Roles (optional)

If the demo will be used by other people, create an admin role and a user role (see `sql/01_roles.sql`). The user role gets SELECT on tables and semantic views, USAGE on the warehouse, the schema and the agent. Ask for the usernames to grant. This needs a role that can create roles.

### Step 11: Demo script

Load `references/demo-script-template.md`. Write a timed script: opening (30 s), context (1 min), questions 1 and 2 (2 min), questions 3 and 4 (3 min), questions 5 and 6 (3 min), closing (1 min). For each question include the exact wording, the expected answer, talking points and the transition. Save it as a local file. ⚠️ **STOP for review.**

## Stopping points

- Step 1B: customer website missing
- Step 2: audience brief
- Step 3: use case
- Step 5: data model
- Step 6: data pattern verification
- Step 11: demo script

## Output

1. Schema `<DATABASE>.<DEMO_NAME>`
2. Populated structured and unstructured tables
3. Semantic view with verified queries
4. Active Cortex Search service
5. Cortex Agent
6. Six sample questions
7. Demo script file

## Troubleshooting

**Semantic view creation fails:** metrics must not have `data_type`; use `SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML`, not `CREATE SEMANTIC VIEW ... FROM @stage`; referenced tables need a `primary_key`; check the tables and columns exist.

**Search service not activating:** check the warehouse, check the text column is not NULL, wait for indexing.

**Agent creation fails:** check tool types are one of `cortex_analyst_text_to_sql`, `cortex_search`, `data_to_chart`, `web_search`, `generic`; confirm cross-region inference is on.

**Agent not responding:** test each tool alone; check the role has USAGE on the agent.

**Email fails:** the recipient must be a verified email address in the account; the notification integration must exist (`sql/00_setup.sql`).

**Data looks unrealistic:** re-research scale, use normal or Poisson distributions, keep planted patterns subtle.
