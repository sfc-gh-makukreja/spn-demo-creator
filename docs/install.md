# Install the skill

The easiest way is to add it from GitHub in CoCo Desktop. CoCo clones the repo and finds the skill for you.

## Option 1: CoCo Desktop (recommended)

1. Open **Agent Settings** (left sidebar) and select **Skills**.
2. In the **GitHub Skills** section, click **+** (Add from GitHub).
3. Enter the source and click **Add**:

    ```text
    sfc-gh-makukreja/spn-demo-creator
    ```

    You can also use `https://github.com/sfc-gh-makukreja/spn-demo-creator`, or pin a branch with `sfc-gh-makukreja/spn-demo-creator#main`.

4. The skill `spn-demo-creator` now appears under **GitHub Skills**.
5. To get later changes, select the skill and click **Update**, or use **Update all**.

<!-- screenshot: install-01-add-from-github.png -->
<!-- screenshot: install-02-skill-listed.png -->

!!! note "Shared with the CLI"
    Skill settings are stored in `~/.snowflake/cortex/skills.json` and shared between CoCo Desktop and the `cortex` command-line tool. A skill added in one is available in the other.

## Option 2: Local folder

Use this if you cannot reach GitHub or want to edit the skill.

1. Clone the repo:

    ```bash
    git clone https://github.com/sfc-gh-makukreja/spn-demo-creator.git ~/snowflake-skills/spn-demo-creator
    ```

2. In **Agent Settings > Skills**, click **+** in the **Local Skills** section and pick `~/snowflake-skills`.

CoCo scans up to 10 folders deep for a file named exactly `SKILL.md`.

## Option 3: Share inside your company

To give your own team the skill, publish it from the Skills tab: **Publish to stage** (a Snowflake stage you control) or **Publish to Skills Catalog** (a versioned object, with access through Snowflake roles).

## Run it

In chat, type `/` and pick `spn-demo-creator`, or ask for a demo and the agent picks the skill from its description:

> Create a CoWork demo for the retail industry

or

> Create a CoWork demo for https://www.example.com

You will see a "Using skill spn-demo-creator" line in the chat. Click it to open the `SKILL.md` the agent loaded.

<!-- screenshot: install-03-first-prompt.png -->

## If the skill does not show up

- Check it appears in **Agent Settings > Skills**.
- Check it is not in `snowflake.skills.disabledSkills`.
- If the clone fails, run `git clone <url>` in a terminal first to confirm your Git credentials.
