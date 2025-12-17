# Error Handling and Fallbacks (SUIP v0.1)

## Purpose

This document defines fallback behavior when errors occur in SUIP-driven systems.

---

## Error Scenarios

### 1. SUIP Validation Failure

**Behavior:**
- Renderer rejects the document
- Logs validation errors
- Displays a safe fallback UI

**Fallback UI:**
- Generic error notice
- Persistent chat remains available

---

### 2. Action Execution Failure

**Behavior:**
- Apply `onError` outcome if present
- Otherwise show a generic error toast

---

### 3. Orchestrator / LLM Timeout

**Behavior:**
- Renderer shows loading timeout notice
- User may retry via chat

---

### 4. Network Failure

**Behavior:**
- Display offline or retry notice
- Preserve current state

---

## Non-Negotiable Rules

- Chat input must remain available
- Renderer must never crash
- Sensitive errors must not be exposed to users

---

## Summary

Fallbacks ensure that **conversation continuity** is preserved even when dynamic UI generation fails.

