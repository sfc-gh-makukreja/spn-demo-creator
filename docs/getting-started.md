# Prerequisites

| Need | Why | How |
|---|---|---|
| A Snowflake account with Cortex Agents and Cortex Search | The demo runs on them | Check with your account team |
| A role that can create a database, schema, warehouse, search service and agent | The skill creates these | Run `sql/00_setup.sql` as ACCOUNTADMIN, then grant a demo role |
| Cross-region inference, if your region lacks the orchestration model | The agent uses `orchestration: auto` | `ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION';` |
| Web search for agents | The `web_search` tool | Snowsight: AI & ML > Agents > Settings > Web search |
| Email integration (optional) | The `send_email` tool | `sql/00_setup.sql`. Recipients must be verified in the account |
| Cortex Code | Runs the skill | Install Cortex Code and connect to your account |

## One-time account setup

Run [`spn-demo-creator/sql/00_setup.sql`](https://github.com/sfc-gh-makukreja/spn-demo-creator/blob/main/spn-demo-creator/sql/00_setup.sql) as ACCOUNTADMIN. It creates:

- database `COWORK_DEMOS`
- warehouse `COWORK_DEMO_WH`
- notification integration `COWORK_DEMO_EMAIL`
- procedure `COWORK_DEMOS.PUBLIC.SEND_MAIL`

Use your own names if you prefer, and tell the skill when it asks.

<!-- screenshot: 00-setup-sql.png -->
