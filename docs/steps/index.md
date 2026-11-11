# Step by step

This walkthrough follows a real run of the skill for **EBOS Group Limited**, a healthcare and pharmaceutical distributor. Every screenshot is from that run, so the numbers and names you see are what the skill produced.

The skill has eleven steps and six approval stops. The whole run took about 20 minutes, most of it clicking **Allow** and approving the stops.

| Step | What | You do |
|---|---|---|
| [0](00-mode.md) | Choose the mode | Send the first prompt |
| [1](01-research.md) | Research | Confirm the summary |
| [2](02-audience.md) | Audience | Choose roles only, or skip |
| [3](03-use-case.md) | Use case | Pick one of three, approve, give names |
| [4](04-schema.md) | Create the schema | Allow the SQL |
| [5](05-data-model.md) | Design the data model | Approve the model |
| [6](06-data.md) | Generate the data | Allow the SQL, review the pattern checks |
| [7](07-semantic-view.md) | Semantic view | Allow the SQL |
| [8](08-search.md) | Cortex Search | Allow the SQL |
| [9](09-agent.md) | Cortex Agent | Allow the SQL |
| [10](10-questions.md) | Sample questions | Read the summary |
| [11](11-script.md) | Demo script | Review the script |

!!! tip "Before you start"
    Open a project folder in CoCo Desktop first. The skill saves `plan.md` and the demo script there. With no folder open, files can end up in your home directory.

!!! note "Allow prompts"
    CoCo asks you to **Allow** each SQL statement. The small arrow next to **Allow** lets you allow similar statements for the session. Read the first few before you allow them all.

See [Gotchas](../gotchas.md) for everything that went wrong or surprised us.
