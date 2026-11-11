# Troubleshooting

## Semantic view creation fails

- Metrics must not have `data_type`.
- Use `SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML`, not `CREATE SEMANTIC VIEW ... FROM @stage`.
- Tables used in relationships need a `primary_key`.
- Check that tables and columns exist.

## Search service not active

- Check the warehouse.
- Check the text column is not NULL.
- Wait two to three minutes for indexing.

## Agent creation fails

- Valid tool types are `cortex_analyst_text_to_sql`, `cortex_search`, `data_to_chart`, `web_search` and `generic`.
- Custom tools need `type`, `execution_environment` and `identifier`.
- Check cross-region inference is on.

## Agent does not respond

- Test each tool alone.
- Check the role has USAGE on the agent.

## Email fails

- The recipient must be a verified email address in the account.
- The integration and procedure must exist (`sql/00_setup.sql`).

## Data looks unrealistic or the story is missing

- Re-run the pattern checks and adjust the thresholds.
- Use normal or Poisson distributions and keep patterns subtle.

!!! tip
    Snowflake syntax changes. If a statement fails, check the current documentation.
