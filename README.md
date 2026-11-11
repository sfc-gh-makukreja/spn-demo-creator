# spn-demo-creator

A Cortex Code skill that builds a Snowflake CoWork demo end to end: synthetic data, a semantic view, a Cortex Search service, a Cortex Agent and a timed demo script. It works for a named customer or for a whole industry.

Sanitised from an internal skill for Snowflake partners. The workflow is the same, but nothing is tied to a specific account, connection or shared database, and the supporting files are included.

## What you get

1. A schema with structured and unstructured synthetic data
2. A semantic view with verified queries
3. A Cortex Search service
4. A Cortex Agent (Analyst, Search, web search, charts, optional email)
5. Six demo questions that go from what, to why, to how
6. A demo script you can present in about 10 minutes

## Install

Copy this folder into your Cortex Code skills folder, for example `~/.snowflake/cortex/skills/spn-demo-creator/`, then ask Cortex Code: "Create a CoWork demo for the <industry> industry".

## Before you run it

1. Read `references/prerequisites.md`.
2. As ACCOUNTADMIN, run `sql/00_setup.sql` (database, warehouse, optional email tool).
3. Make sure your connection points at the account you want to build in. The skill asks you to confirm.

## Two modes

- **Industry:** you pick an industry. The skill researches trends and pain points and proposes three use cases. Good for workshops.
- **Customer:** you give a company website. The skill researches the company and tailors the use cases. Researching named people is optional and uses public information only.

## Layout

```
SKILL.md                         the workflow
sql/00_setup.sql                 one-time account setup
sql/01_roles.sql                 optional roles for sharing a demo
sql/99_teardown.sql              remove a demo
references/prerequisites.md
references/data-templates.md
references/agent-spec-template.yaml
references/demo-script-template.md
```

## Notes

- All data is synthetic. Do not load real customer or personal data.
- Snowflake syntax changes. If a statement fails, check the current documentation.
- Unofficial. Not supported by Snowflake.
