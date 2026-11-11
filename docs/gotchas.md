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
