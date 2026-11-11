# Gotchas

Everything that went wrong or surprised us in the first run, in one place.

## Before you start

!!! warning "Open a project folder"
    With no folder open, `plan.md` and the demo script are written to your home directory.

!!! warning "Desktop 'Add from GitHub' can fail"
    `Error adding skill: process is not defined`. Install with `cortex skill add sfc-gh-makukreja/spn-demo-creator` in a terminal instead. See [Install](install.md).

!!! note "The setup SQL is optional"
    You only need `sql/00_setup.sql` for the email tool or the default names. The skill creates the database, warehouse and schema itself if your role allows it.

## While it runs

!!! note "Allow, again and again"
    Every SQL statement needs **Allow**. The arrow beside it allows similar statements for the session.

!!! note "A failed first query is normal"
    The existence check in step 4 shows as failed.

!!! warning "A table can fail silently"
    `DIM_PRODUCT` was missing after a multi-statement call. Run one statement per call, use fully qualified names, and check every table exists.

!!! warning "The 'no effect' dimension can be noisy"
    Random assignment gave 18 to 32% across warehouses. Assign it round-robin within each segment.

!!! note "'Could not fully classify one or more statements'"
    Shown for the semantic view call. It is expected; click **Allow**.

## What to check at the end

!!! warning "Verified queries and web search"
    This run created the semantic view without verified queries and the agent without `web_search`. Both are fixed in the skill, but check the final report for anything it says it left out.

!!! warning "The agent was not asked a question"
    Only Cortex Analyst was tested. Use step 12 (test and publish) or ask question 1 in CoWork before you present.

!!! warning "Agent missing from CoWork"
    If the account already has `SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT`, add the agent to it with `ALTER SNOWFLAKE INTELLIGENCE ... ADD AGENT`.

!!! danger "Public repo, synthetic data only"
    Screenshots can show connection names and usernames. Redact them before you publish.

## If something fails

### Semantic view creation fails

- Metrics must not have `data_type`.
- Use `SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML`, not `CREATE SEMANTIC VIEW ... FROM @stage`.
- Tables used in relationships need a `primary_key`.
- Check that tables and columns exist.

### Search service not active

- Check the warehouse.
- Check the text column is not NULL.
- Wait two to three minutes for indexing.

### Agent creation fails

- Valid tool types are `cortex_analyst_text_to_sql`, `cortex_search`, `data_to_chart`, `web_search` and `generic`.
- Custom tools need `type`, `execution_environment` and `identifier`.
- Check cross-region inference is on.

### Agent does not respond

- Test each tool alone.
- Check the role has USAGE on the agent.

### Email fails

- The recipient must be a verified email address in the account.
- The integration and procedure must exist (`sql/00_setup.sql`).

### Data looks unrealistic or the story is missing

- Re-run the pattern checks and adjust the thresholds.
- Use normal or Poisson distributions and keep patterns subtle.

!!! tip
    Snowflake syntax changes. If a statement fails, check the current documentation.
