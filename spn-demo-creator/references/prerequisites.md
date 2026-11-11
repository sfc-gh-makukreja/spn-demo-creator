# Prerequisites

| Need | Why | How |
|---|---|---|
| A Snowflake account with Cortex Agents and Cortex Search | The demo runs on them | Check with your account team |
| A role that can create a database, schema, warehouse, search service and agent | The skill creates these | Use `sql/00_setup.sql` as ACCOUNTADMIN, then grant a demo role |
| Cross-region inference, if your region lacks the orchestration model | The agent uses `orchestration: auto` | `ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION';` (ACCOUNTADMIN) |
| Web search for agents | The `web_search` tool | Snowsight: AI & ML > Agents > Settings > Web search |
| Email integration (optional) | The `send_email` tool | `sql/00_setup.sql`. Recipients must be verified in the account |
| A coding agent that can run SQL and web search | Runs the workflow | Cortex Code, with this folder installed as a skill |

Confirm the active connection and role with the user before running anything. Use your own database and warehouse names if you do not want the defaults (`COWORK_DEMOS`, `COWORK_DEMO_WH`).

Do not put real customer data or personal data in a demo. All data is synthetic.
