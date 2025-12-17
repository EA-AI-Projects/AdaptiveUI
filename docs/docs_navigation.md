# Navigation Model (SUIP v0.1)

## Purpose

This document defines how **scene navigation** works in SUIP-based applications.

---

## Core Concepts

- A **scene** represents a single SUIP document
- Renderers maintain a **navigation stack** of scenes

---

## Navigation Types

### Navigate (Push)

```json
{ "type": "navigate", "targetSceneId": "catalog_latest" }
```

- Pushes a new scene onto the stack

---

### Replace

Renderers MAY treat `navigate` as replace if configured (platform-specific).

---

### Back

Handled via allowlisted action:

```json
{ "actionId": "ui.back" }
```

- Pops current scene
- No-op if already at root

---

### Home

```json
{ "actionId": "ui.goHome" }
```

- Clears stack
- Navigates to home/root scene

---

## Rules

- Navigation is deterministic
- LLM never manipulates the stack directly
- Renderer owns history

---

## Summary

SUIP navigation favors **simple, predictable scene transitions** over complex routing logic.

