# Demo Script Template

## Format

```markdown
# Snowflake CoWork Demo: <CUSTOMER_OR_INDUSTRY>
## Use Case: <USE_CASE_TITLE>

---

## Opening (30 seconds)

**Hook:** <1-2 sentences connecting to a real business challenge the customer faces>

**Setup:** "Let me show you how Snowflake CoWork can help <CUSTOMER_OR_INDUSTRY> get instant answers from your data — structured analytics, unstructured insights, and real-time context — all through a single conversational interface."

---

## Context (1 minute)

**What they're seeing:**
- Single agent connected to your data (structured tables + documents)
- Natural language interface — no SQL needed
- Professional visualizations generated automatically
- Can take actions (send emails, create reports)

**Data in the demo:**
- <Brief description of structured tables>
- <Brief description of unstructured data>
- Real-time web search for external context

---

## Question 1: <DESCRIPTIVE - What>

**Type:** Descriptive Analytics (simple fact retrieval)

**Ask:** "<Exact question to type>"

**Expected answer:** <What the agent should return — metric, chart, table>

**Talking points while waiting:**
- "Notice how it's querying your structured data automatically..."
- "No need to know which table or write SQL..."

**Talking points after answer:**
- <What to highlight in the response>
- <Transition to next question>

---

## Question 2: <DESCRIPTIVE - What/Trend>

**Type:** Descriptive Analytics (trend/comparison)

**Ask:** "<Exact question to type>"

**Expected answer:** <Trend chart, comparison table>

**Talking points while waiting:**
- "Now let's look at this over time..."

**Talking points after answer:**
- "You can see <pattern> here..."
- "This naturally leads us to ask — why is this happening?"

---

## Question 3: <DIAGNOSTIC - Why>

**Type:** Diagnostic Analytics (root cause)

**Ask:** "<Exact question to type>"

**Expected answer:** <Multi-factor analysis, root cause breakdown>

**Talking points while waiting:**
- "This is where it gets powerful — the agent will analyze multiple factors..."

**Talking points after answer:**
- "It identified <N> contributing factors..."
- "Notice it's pulling from both structured data AND unstructured sources..."

---

## Question 4: <DIAGNOSTIC - Why (deeper)>

**Type:** Diagnostic Analytics (multi-factor / cross-source)

**Ask:** "<Exact question to type>"

**Expected answer:** <Cross-referencing structured + unstructured data>

**Talking points while waiting:**
- "Now it's searching through documents and combining with the metrics..."

**Talking points after answer:**
- "This is the power of combining Cortex Analyst with Cortex Search..."
- "Traditional BI would require a human analyst to connect these dots..."

---

## Question 5: <PRESCRIPTIVE - How/Recommendation>

**Type:** Prescriptive Analytics (actionable recommendation)

**Ask:** "<Exact question to type>"

**Expected answer:** <Specific recommendations with expected impact>

**Talking points while waiting:**
- "Let's move from diagnosis to action..."

**Talking points after answer:**
- "These aren't generic suggestions — they're grounded in YOUR data..."
- "Each recommendation has supporting evidence..."

---

## Question 6: <PRESCRIPTIVE - Action + Email>

**Type:** Prescriptive Analytics (action-taking)

**Ask:** "<Exact question — should trigger email composition>"

**Expected answer:** <Recommendation + email draft sent>

**Talking points while waiting:**
- "Watch how it goes beyond analysis to actually TAKE ACTION..."

**Talking points after answer:**
- "It just drafted and sent a professional email based on the analysis..."
- "This is the future — AI that doesn't just inform, but acts..."

---

## Closing (1 minute)

**Summary:**
"In just a few minutes, we went from basic metrics → root cause analysis → specific recommendations → automated action. All conversational, all grounded in YOUR data."

**Capabilities demonstrated:**
1. Natural language queries over structured data (Cortex Analyst)
2. Semantic search over documents and feedback (Cortex Search)
3. Real-time external context (Web Search)
4. Professional visualizations (auto-generated charts)
5. Action-taking (email delivery)

**Next steps:**
- "We can connect this to your actual data in days, not months"
- "What questions would YOU want to ask your data?"
- "Let's discuss a proof-of-concept with your real datasets"

---

## Technical Notes (for presenter)

- **Agent location:** COWORK_DEMOS.<CUSTOMER_OR_INDUSTRY>.<AGENT_NAME>
- **Access URL:** Snowsight > AI & ML > Agents > <AGENT_NAME>
- **Fallback:** If agent is slow, have pre-cached answers ready
- **Web search:** Ensure enabled before demo (AI & ML > Agents > Settings)
- **Timing:** Total demo ~10 minutes with talking points
```

## Script Writing Guidelines

1. **Personalize the hook** — reference something specific from customer research
2. **Questions should build narratively** — each answer sets up the next question
3. **Keep talking points brief** — 1-2 sentences while waiting, 1-2 after answer
4. **Highlight cross-tool moments** — when agent combines Analyst + Search + Web
5. **End with action** — Q6's email (or action plan, if email is off) shows CoWork isn't just analytics, it's automation
6. **Include fallback plan** — note expected answers in case agent gives different response
