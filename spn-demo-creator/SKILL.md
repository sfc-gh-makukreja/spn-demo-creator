---
name: spn-demo-creator
description: "Create Snowflake CoWork (Snowflake Intelligence) demos with synthetic data, a semantic view, a Cortex Search service, a Cortex Agent and a ready-to-present demo script, for a named customer or for a whole industry. Use this skill whenever the user wants to build or prepare a CoWork, Snowflake Intelligence or Cortex Agent demo, a talk track or a use case for one, even if they only name a company or industry and do not say CoWork. Triggers: CoWork demo, Snowflake Intelligence demo, Cortex Agent demo, partner demo, industry demo, customer demo for CoWork, demo script for CoWork."
---

# CoWork Demo Creator

Create an end-to-end Snowflake CoWork demo: realistic synthetic data, a semantic view, a Cortex Search service, a Cortex Agent and a timed demo script. Works in two modes.

| Mode | Use when | Research |
|---|---|---|
| **Industry** | No named customer yet, or a workshop or lunch and learn | Industry trends, common data pain points, typical personas |
| **Customer** | A named prospect or customer | Company website, public news, optional audience research |

## When to use

Use this skill when the user wants a demo that shows an agent answering questions over structured data and documents. Do not use it for a generic slide deck, a dashboard, or a demo of a feature other than CoWork and Cortex Agents.

## Where files are

`references/` and `sql/` are folders next to this SKILL.md. Resolve them against this skill's own folder, not the current project, because the skill may be installed from a cache and the project folder will not contain them.

## Parameters

Ask the user for these once, at the start, and reuse them. Offer the defaults.

| Parameter | Default | Meaning |
|---|---|---|
| `<DATABASE>` | `COWORK_DEMOS` | Database that holds demo schemas |
| `<WAREHOUSE>` | `COWORK_DEMO_WH` | Warehouse for search, agent tools and queries |
| `<DEMO_NAME>` | derived in Step 4 | Schema name for this demo |
| Connection | none | The Snowflake connection to use. Never assume one; confirm before creating anything |

## Prerequisites

Read `references/prerequisites.md`. In short:
- A role that can create a database, schema, warehouse, Cortex Search service and Cortex Agent
- Cross-region inference enabled if the region lacks the orchestration model
- Web search enabled for agents in Snowsight (AI & ML > Agents > Settings)
- Optional: the email tool, set up by `sql/00_setup.sql`

These need ACCOUNTADMIN. Do not change account-level settings yourself. Show the user the command, explain why, and wait for them to confirm they ran it.

## Workflow

Do not create any Snowflake object until the use case is approved in Step 3. Steps 0 to 3 are research and conversation only. Where a step needs the user's input, use the `ask_user_question` tool if it is available; otherwise ask in chat.

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
3. If web search is unavailable, say so, use what you know, and mark the findings as unverified.
4. Summarize findings and ask the user to confirm or correct them.

**Output:** Industry context summary

### Step 1B: Customer mode

1. If the user has not given the customer's website, ⚠️ **STOP** and ask for it.
2. Research with web search: company overview, products and services, geography, recent news, competitors, data-intensive processes. If web search is unavailable, say so and mark findings as unverified.
3. Summarize findings for confirmation.

**Output:** Customer context summary

### Step 2: Audience (optional)

Ask whether the user wants to tailor the demo to specific attendees.

- **Industry mode:** describe the roles in the room (for example "partner SEs and solution architects"). Do not research named individuals.
- **Customer mode:** only research people if the user provides them, using public professional information only. Do not infer anything sensitive about a person. If the user prefers, describe the roles instead.

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

⚠️ **STOP for approval before building.** Nothing is created in Snowflake before this approval.

### Step 4: Create the schema

1. Sanitize the name (uppercase, underscores, no special characters). Use the customer name, or the industry and use case in industry mode.
2. Check whether the schema exists. If so, ask the user for another name. Never replace an object this skill did not create.
3. Run (use `USE DATABASE` before `CREATE SCHEMA`; right after `CREATE DATABASE` the schema statement can race):
   ```sql
   CREATE DATABASE IF NOT EXISTS <DATABASE>;
   USE DATABASE <DATABASE>;
   CREATE SCHEMA IF NOT EXISTS <DATABASE>.<DEMO_NAME>;
   USE SCHEMA <DATABASE>.<DEMO_NAME>;
   ```
4. Write a short `plan.md` in the user's project folder with the mode, use case, names and the story (the three acts from Step 5). It is the record the user reviews at Step 6, so do not skip it. If no project folder is open, ask the user where to save files before writing anything.

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
- 200 to 1000 rows per structured table and 50 to 250 documents. Dates in the last 12 to 24 months.
- Plant the story contract from Step 5. Do not use plain random values for the driver columns; use one random roll per row and thresholds per segment (see `references/data-templates.md`) so the target rates hold.
- Run one statement per SQL call and use fully qualified names (`<DATABASE>.<DEMO_NAME>.<TABLE>`). A script of several `CREATE TABLE ... AS` statements can fail partway without an obvious error (one table was missing in testing), and later steps then break.
- Create tables in dependency order (dimensions first), then load the fact tables, because facts depend on dimension helper columns.
- After the loads, confirm every table exists and has the expected row count before going on.
- Make the dimension you want to rule out (Step 5, act 2) balanced by construction, for example by assigning it round-robin within each driver segment. Random assignment on a few hundred rows gives noisy rates (18 to 32 percent in testing) and the act then fails to show "no effect".
- **Verify the patterns.** Run one GROUP BY query per act (overall split, the ruled-out dimension, each driver) and compare to the target rates. If they are significantly off, adjust the thresholds and reload. ⚠️ **STOP and show the user the verification results.**

Steps 7 to 10 then run in sequence. Stop at the first error and report it. They create objects only inside the demo schema.

### Step 7: Create the semantic view

Use the `semantic-view` skill if available. Rules that commonly trip people up:

- Metrics use an aggregation in `expr` (for example `SUM(col)`) and have no `data_type`.
- Dimensions, time dimensions and facts must have `data_type`.
- Relationships use `relationship_columns`. Referenced tables must have a `primary_key`.
- Include 8 verified queries (the 6 demo questions plus 2 extras) and one business metric that shows a KPI calculation.

Add the verified queries to the YAML before you create the view. Do not defer them; the final report must not say they are missing.

Create the view from the YAML with `SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML`. The inline form is the one tested, so use it first (the third argument `FALSE` means create, not verify only). The tool may report "could not fully classify one or more statements; treated as a write" and ask the user to Allow it; that is expected.
```sql
CALL SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML('<DATABASE>.<DEMO_NAME>', $$<YAML>$$, FALSE);
```
If the inline form fails, try the stage form:
```sql
CREATE STAGE IF NOT EXISTS <DATABASE>.<DEMO_NAME>.SEMANTIC_MODELS;
-- upload the YAML with PUT or snow stage copy
CREATE OR REPLACE FILE FORMAT <DATABASE>.<DEMO_NAME>.YAML_FF
  TYPE = CSV FIELD_DELIMITER = NONE RECORD_DELIMITER = NONE;
CALL SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML(
  '<DATABASE>.<DEMO_NAME>',
  (SELECT $1 FROM @<DATABASE>.<DEMO_NAME>.SEMANTIC_MODELS/<file>.yaml
    (FILE_FORMAT => '<DATABASE>.<DEMO_NAME>.YAML_FF')));
```
Verify with `SHOW SEMANTIC VIEWS IN SCHEMA <DATABASE>.<DEMO_NAME>` and test with Cortex Analyst.

### Step 8: Create the Cortex Search service

```sql
CREATE OR REPLACE CORTEX SEARCH SERVICE <DATABASE>.<DEMO_NAME>.<SEARCH_NAME>
  ON <text_column>
  ATTRIBUTES <filterable_columns>
  WAREHOUSE = <WAREHOUSE>
  TARGET_LAG = '1 hour'
  AS (SELECT * FROM <unstructured_table>);
SHOW CORTEX SEARCH SERVICES IN SCHEMA <DATABASE>.<DEMO_NAME>;
```
Wait 2 to 3 minutes for indexing. The service must be ACTIVE.

### Step 9: Create the Cortex Agent

1. You must have a one or two sentence agent description. Ask if missing. It becomes the agent `COMMENT`.
2. Start from `references/agent-spec-template.yaml` and fill in the names. Tools: Cortex Analyst (`cortex_analyst_text_to_sql`), Cortex Search (`cortex_search`), `data_to_chart`, `web_search` (include it unless web search is not enabled for agents), and optionally `send_email`, a `generic` tool that calls `<DATABASE>.PUBLIC.SEND_MAIL` (created by `sql/00_setup.sql`). In the final report, list every tool you left out and why.
3. Custom tools must use type `generic`, and their `tool_resources` need `type`, `execution_environment` and `identifier`.
4. Include at least 5 `sample_questions` that follow the demo story (what, what, why, why, how).
5. Create the agent in the demo schema with `CREATE OR REPLACE AGENT ... COMMENT = '...' FROM SPECIFICATION $$...$$`.
6. Validate with `SHOW AGENTS IN SCHEMA <DATABASE>.<DEMO_NAME>`, then ask the agent question 1. Testing only the semantic view with Cortex Analyst does not prove the agent works. One way is `SELECT SNOWFLAKE.CORTEX.DATA_AGENT_RUN('<DATABASE>.<DEMO_NAME>.<AGENT_NAME>', $${"messages":[{"role":"user","content":[{"type":"text","text":"<question 1>"}]}]}$$);`. If you cannot run it, say so and ask the user to test in Snowsight. If the agent does not appear in Snowsight, tell the user and check the current documentation for where agents must be created for their account. If it fails at runtime, check that the semantic view and the search service are both active and that the role has USAGE on the agent.

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

### Optional: Roles

If other people will use the demo, create an admin role and a user role (see `sql/01_roles.sql`). Ask for the usernames to grant. This needs a role that can create roles.

### Step 11: Demo script

Load `references/demo-script-template.md`. Write a timed script: opening (30 s), context (1 min), questions 1 and 2 (2 min), questions 3 and 4 (3 min), questions 5 and 6 (3 min), closing (1 min). For each question include the exact wording, the expected answer, talking points and the transition. Save it in the user's project folder (ask for a folder if none is open; do not write to the home directory). ⚠️ **STOP for review.**

### Step 12: Test and publish to CoWork

1. Ask each demo question with `cortex agents run <DATABASE>.<DEMO_NAME>.<AGENT_NAME> "<question>"` (the `agent-studio` skill does this if available). Report any answer that does not match the story contract.
2. Set a display name: `ALTER AGENT ... SET PROFILE = '{"display_name": "<name>"}'`.
3. Run `SHOW SNOWFLAKE INTELLIGENCES`. If `SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT` exists, run `ALTER SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT ADD AGENT <fqn>`. Otherwise create it first.
4. Give the user `https://ai.snowflake.com` and the display name. State that the agent runs under the user's own role; other users need grants (see `sql/01_roles.sql`).
5. ⚠️ Run each statement only after the user clicks Allow. Never include account identifiers or usernames in anything you save.

## Stopping points

- ✋ Step 1B: customer website missing
- ✋ Step 2: audience brief
- ✋ Step 3: use case (nothing is created before this)
- ✋ Step 5: data model
- ✋ Step 6: data pattern verification
- ✋ Step 11: demo script

## Output

1. Schema `<DATABASE>.<DEMO_NAME>`
2. Populated structured and unstructured tables
3. Semantic view with verified queries
4. Active Cortex Search service
5. Cortex Agent
6. Six sample questions
7. Demo script file and `plan.md`

## Success criteria

- Row counts match the plan and the Step 6 pattern checks are within range of the targets.
- `SHOW SEMANTIC VIEWS`, `SHOW CORTEX SEARCH SERVICES` and `SHOW AGENTS` all list the objects, and the search service is ACTIVE.
- The agent answers question 1 with the expected number and split.

## Troubleshooting

- **Semantic view fails:** see the rules in Step 7; check the tables and columns exist.
- **Search service not active:** check the warehouse and that the text column is not NULL; wait for indexing.
- **Agent creation fails:** check tool types (`cortex_analyst_text_to_sql`, `cortex_search`, `data_to_chart`, `web_search`, `generic`) and that cross-region inference is on.
- **Email fails:** the recipient must be a verified email in the account, and the integration and procedure must exist (`sql/00_setup.sql`).
- **Data looks unrealistic:** re-research scale, use normal or Poisson distributions, keep planted patterns subtle.
