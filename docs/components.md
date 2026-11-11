# Components

| Component | What it is | Created in step |
|---|---|---|
| Skill (`SKILL.md`) | The workflow Cortex Code follows | n/a |
| `sql/00_setup.sql` | Database, warehouse, email integration, `SEND_MAIL` | Before you start |
| `sql/01_roles.sql` | Optional admin and user roles | After step 10 |
| `sql/99_teardown.sql` | Removes a demo | When finished |
| `references/data-templates.md` | Table patterns, how to plant the story, verification queries | Steps 5 and 6 |
| `references/agent-spec-template.yaml` | Agent specification to start from | Step 9 |
| `references/demo-script-template.md` | Format of the demo script | Step 11 |
| `references/prerequisites.md` | Account requirements | Before you start |

## What the skill creates for each demo

| Object | Name |
|---|---|
| Schema | `COWORK_DEMOS.<DEMO_NAME>` |
| Structured tables | 4 to 6, with a planted story |
| Document table | 1 or 2, with 50 to 250 documents |
| Semantic view | with 8 verified queries |
| Search service | on the document text |
| Agent | Analyst, Search, web search, charts, optional email |
| Files | `plan.md`, the semantic view YAML, the agent spec, the demo script |
