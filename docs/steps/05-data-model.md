# 5. Design the data model

**Goal:** Design tables that can tell the story.

## What happens

The skill wrote the **story contract**, the numbers the data must produce, and proposed the tables:

1. **What:** about 22% of weekly stock positions for essential medicines are stock-outs over 18 months.
2. **Rule out the obvious:** warehouse (Sydney, Melbourne, Brisbane, Perth, Auckland, Christchurch) has no effect, about 22% each.
3. **Real drivers:** supplier lead-time band (Long over 45 days about 45%, Medium 15 to 45 days about 18%, Short under 15 days about 8%) and cold-chain products (about 40% against 17% ambient), which stack.

Tables: `DIM_SUPPLIER` (30), `DIM_PRODUCT` (60), `DIM_WAREHOUSE` (6), `FACT_STOCK_POSITIONS` (about 900), `FACT_PURCHASE_ORDERS` (about 600), and the unstructured `SUPPLIER_NOTICES` (about 150 documents).

![Data model approval](../assets/img/05-data-model.png)

## What you do

Choose **Approve model**, or **Change it** and describe what to change.

!!! tip "Read the story contract"
    The three acts and their target rates are the point of the demo. If they do not match the story you want to tell, change them here.

!!! warning "`plan.md` was skipped"
    The skill said it did not need a `plan.md`, even though it should write one. This is fixed in the skill.
