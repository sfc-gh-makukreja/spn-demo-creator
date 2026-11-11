# Adding screenshots

Each step page has a slot for a screenshot.

1. Save the image in `docs/assets/img/` using the name shown on the page, for example `04-schema.png`.
2. In the page, replace the "Screenshot to add" block with:

    ```markdown
    ![Create the schema](../assets/img/04-schema.png)
    ```

3. Preview locally:

    ```bash
    python3 -m venv .venv
    .venv/bin/pip install mkdocs mkdocs-material
    .venv/bin/mkdocs serve
    ```

Use PNG, crop to the relevant area, and blur anything that is not synthetic (account names, emails).
