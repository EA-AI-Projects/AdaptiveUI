# Orchestrator Contract (SUIP v0.1)

## Purpose

This document defines the contract between the **renderer** and the **LLM orchestrator**.

---

## Orchestrator Responsibilities

- Maintain conversation history
- Track session state summary
- Enforce manifest constraints
- Prompt LLM to produce SUIP documents

---

## Renderer → Orchestrator Request

```json
{
  "sessionId": "abc123",
  "userMessage": "I want to pay my bill",
  "stateSummary": {
    "currentSceneId": "home_shell",
    "knownIntent": "billing"
  },
  "manifest": { /* UI Manifest */ }
}
```

---

## Orchestrator → Renderer Response

```json
{
  "suip": { /* SUIP document */ },
  "diagnostics": {
    "intent": "billing.pay",
    "confidence": 0.92
  }
}
```

---

## Rules

- Orchestrator must only emit schema-valid SUIP
- Diagnostics are optional and non-rendered
- Renderer MUST validate SUIP before rendering

---

## Summary

The orchestrator translates **conversation → UI intent**, while the renderer remains deterministic and dumb.

