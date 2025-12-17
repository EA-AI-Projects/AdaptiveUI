# Diagrams

This document contains Mermaid diagrams to visually explain AdaptiveUI and SUIP.

> Tip: GitHub renders Mermaid in Markdown automatically.

---

## One-Pager: AdaptiveUI + SUIP (Concept + Architecture)

```mermaid
flowchart LR
  %% ===== CLIENT =====
  subgraph Client[Client Device]
    U[User]
    Shell["AdaptiveUI Shell<br/>(Persistent Chat + Minimal Shell)"]
    RE["Rendering Engine<br/>(Web / iOS / Android / Desktop)"]
    UI["Native UI<br/>(Components + Layout)"]
    U --> Shell
    Shell --> RE
    RE --> UI
    UI --> U
  end

  %% ===== SERVER =====
  subgraph Server[Backend]
    GW[API Gateway]
    ORCH["Orchestrator<br/>(Session + Policy + Prompts)"]
    MAN["UI Manifest Store<br/>(allowlisted components/actions)"]
    STATE["Session State Store<br/>(forms/data/flags)"]
    LLM["LLM Provider<br/>(UI Planner)"]
    ACT["Action Adapters<br/>(Billing/Support/Catalog)"]
  end

  %% ===== PROTOCOL BOUNDARY =====
  SUIP["SUIP Document<br/>(Structured UI Intent)"]

  %% ===== FLOWS =====
  Shell -->|chat + events| GW --> ORCH
  ORCH --> MAN
  ORCH <--> STATE
  ORCH --> LLM
  LLM -->|generates| SUIP
  ORCH -->|returns SUIP| GW --> RE

  RE -->|action request| GW --> ORCH --> ACT
  ACT -->|result + data| ORCH

  %% ===== ANNOTATIONS =====
  MAN -. constrains .-> LLM
  MAN -. constrains .-> ORCH
  MAN -. constrains .-> RE
```

---

## Sequence Diagram: Core Runtime Loop

```mermaid
sequenceDiagram
  autonumber
  participant User
  participant Shell as AdaptiveUI Shell (Chat)
  participant Renderer as Rendering Engine
  participant Orch as Orchestrator
  participant LLM as LLM (UI Planner)
  participant Manifest as UI Manifest
  participant Actions as Action Adapters

  User->>Shell: Types intent (e.g., "I want to pay my bill")
  Shell->>Orch: Send userMessage + stateSummary + currentSceneId
  Orch->>Manifest: Load allowlisted components/actions
  Orch->>LLM: Prompt (chat + state + manifest constraints)
  LLM-->>Orch: SUIP JSON (schema-valid)
  Orch-->>Renderer: SUIP document
  Renderer->>Renderer: Validate SUIP against JSON Schema
  Renderer-->>User: Render UI (chat remains persistent)

  User->>Renderer: Interacts (click/submit)
  Renderer->>Renderer: Resolve bindings (e.g., {{payForm.amount}})
  Renderer->>Orch: Action request {actionId, params}
  Orch->>Manifest: Validate action + params schema
  Orch->>Actions: Execute allowlisted action adapter
  Actions-->>Orch: Result {status, data, optional outcome}
  Orch-->>Renderer: Action response
  Renderer-->>User: Apply outcome (navigate/toast/setState)

  Note over User,Renderer: Loop continues: chat or UI events can trigger next SUIP
```

## Adaptive UI High-Level Concept
```mermaid
flowchart LR
  U[User] --> C[Persistent Chat + Minimal Shell]
  C --> O["Orchestrator<br/>(intent + policy + state)"]
  O --> LLM["LLM<br/>(UI planner)"]
  LLM --> SUIP["SUIP Document<br/>(structured UI intent)"]
  SUIP --> R["Rendering Engine<br/>(web / iOS / android)"]
  R --> UI["Native UI Components"]
  UI --> U

  UI -->|events| R
  R -->|action requests| O
```

## What SUIP is and does (protocol boundary)
```mermaid
flowchart TB
  subgraph "LLM / Planning Side"
    LLM["LLM produces intent"]
    M["UI Manifest<br/>(allowed components + actions)"]
    LLM -->|constrained by| M
    LLM --> SUIP["SUIP JSON<br/>(versioned schema)"]
  end

  subgraph "Runtime / Execution Side"
    V["Schema Validation"]
    RE["Renderer maps SUIP to native widgets"]
    AA["Action Adapters<br/>(approved APIs)"]
    S["Session State<br/>(forms/data/flags)"]
  end

  SUIP --> V --> RE
  RE -->|user interactions| AA
  AA -->|results + outcomes| RE
  RE <--> S
```

## AdaptiveUI system architecture (end-to-end)
```mermaid
flowchart LR
  subgraph Client["Client Device"]
    Shell["AdaptiveUI Shell<br/>(chat + renderer)"]
    Render["Rendering Engine"]
    Shell --> Render
  end

  subgraph Server["Backend"]
    Gateway["API Gateway"]
    Orchestrator["Orchestrator Service<br/>(policy + session + prompts)"]
    Manifest["Manifest Store<br/>(per app)"]
    State["State Store<br/>(session snapshots)"]
    Adapters["Action Adapter Layer<br/>(billing/support/catalog)"]
    LLM["LLM Provider"]
  end

  Shell -->|chat + events| Gateway --> Orchestrator
  Orchestrator --> Manifest
  Orchestrator <--> State
  Orchestrator --> LLM
  Orchestrator -->|SUIP| Gateway --> Render
  Render -->|action request| Gateway --> Orchestrator --> Adapters
  Adapters --> Orchestrator
```

## SUIP architecture as a whole (spec + implementations)
```mermaid
flowchart TB
  subgraph Spec["SUIP Specification"]
    Schema["SUIP JSON Schema"]
    Semantics["Semantics<br/>(state/binding/actions/nav)"]
    Examples["Example Scenes"]
    Schema --> Semantics --> Examples
  end

  subgraph Manifests["UI Manifests"]
    ManifestSchema["Manifest Schema"]
    AppManifest["App Manifest<br/>(allowlist + param schemas)"]
    ManifestSchema --> AppManifest
  end

  subgraph Engines["Rendering Engines"]
    Web[Web Renderer]
    iOS[iOS Renderer]
    Android[Android Renderer]
    Desktop[Desktop Renderer]
  end

  Spec --> Engines
  Manifests --> Engines
  AppManifest -->|constrains| Web
  AppManifest -->|constrains| iOS
  AppManifest -->|constrains| Android
  AppManifest -->|constrains| Desktop
```