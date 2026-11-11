# Architecture

## The flow

```mermaid
flowchart LR
    U[You] --> CC[Cortex Code + skill]
    CC -->|web search| R[Research]
    R --> UC[Use cases you approve]
    UC --> D[Synthetic data]
    D --> SV[Semantic view]
    D --> CS[Cortex Search service]
    SV --> A[Cortex Agent]
    CS --> A
    WS[Web search] --> A
    CH[Charts] --> A
    EM[SEND_MAIL] --> A
    A --> SF[Snowflake Intelligence / CoWork]
    CC --> DS[Demo script]
```

## What lives where

```mermaid
flowchart TB
    subgraph Account
      subgraph COWORK_DEMOS
        subgraph DEMO_SCHEMA
          T1[Structured tables]
          T2[Document table]
          SV[Semantic view]
          CS[Search service]
          AG[Agent]
        end
        subgraph PUBLIC
          SM[SEND_MAIL procedure]
        end
      end
      WH[COWORK_DEMO_WH]
      NI[COWORK_DEMO_EMAIL integration]
    end
    T1 --> SV
    T2 --> CS
    SV --> AG
    CS --> AG
    SM --> AG
    NI --> SM
```

## How a question is answered

```mermaid
sequenceDiagram
    participant User
    participant Agent
    participant Analyst as Cortex Analyst
    participant Search as Cortex Search
    User->>Agent: Why are we losing in this segment?
    Agent->>Analyst: Break the metric down by segment and time
    Analyst-->>Agent: Rows and a chart
    Agent->>Search: Find documents about that segment
    Search-->>Agent: Matching passages
    Agent-->>User: Answer with charts and cited evidence
```

## Approval gates

The skill stops and asks you at five points so nothing is built that you have not approved: audience brief, use case, data model, data patterns, and the final demo script.
