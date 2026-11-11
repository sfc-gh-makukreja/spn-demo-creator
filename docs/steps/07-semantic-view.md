# 7. Semantic view

**Goal:** Give Cortex Analyst a business-friendly layer over the tables.

## What happens

The skill writes the semantic view YAML (metrics, dimensions, relationships, 8 verified queries) and creates it with `SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML`. It validates with `SHOW SEMANTIC VIEWS` and a test question.

## What you do

> No input needed unless validation fails.

## Output

A working semantic view.

## Screenshot

!!! info "Screenshot to add"
    Save as `docs/assets/img/07-semantic-view.png`, then replace this block with:
    `![7. Semantic view](../assets/img/07-semantic-view.png)`

<!-- screenshot: 07-semantic-view.png -->
