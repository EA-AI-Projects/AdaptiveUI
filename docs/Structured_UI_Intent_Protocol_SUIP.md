# Structured UI Intent Protocol (SUIP)

## Overview

The **Structured UI Intent Protocol (SUIP)** defines a device‑agnostic, LLM‑friendly schema for expressing **UI intent**. It allows large language models to describe *what the user should see and do next*, without prescribing how that UI is implemented on a specific platform.

SUIP plays a role for UI similar to what **Model Context Protocol (MCP)** plays for tool invocation.

---

## Design Goals

- Device‑agnostic rendering
- Deterministic, schema‑validated output
- Safe execution (no arbitrary code)
- Declarative, intent‑first UX
- Portable across web, mobile, desktop, and voice

---

## Conceptual Model

**LLM → SUIP document → Rendering Engine → Native UI**

- LLM plans UI intent
- SUIP expresses that intent
- Rendering engines translate intent into platform‑native widgets

---

## Core Principles

1. **No HTML or JS** in protocol output
2. **Strict schemas** with versioning
3. **Manifest‑driven allowlists** for components and actions
4. **Declarative UI**, imperative logic lives in action adapters

---

## SUIP Document Structure

### Required Fields

- `suipVersion`
- `sceneId`
- `layout`
- `chat`

### Optional Fields

- `title`
- `theme`
- `deviceHints`
- `analytics`

---

## Layout Primitives

Supported layout types:
- `stack`
- `grid`
- `split`
- `tabs`
- `modal`
- `drawer`

Layouts contain child components or nested layouts.

---

## Component Primitives

Common component types:
- `text`
- `image`
- `card`
- `list`
- `table`
- `form`
- `input`
- `button`
- `notice`

Each component:
- Has a strict prop schema
- Supports accessibility metadata

---

## Actions

Actions are first‑class protocol elements.

### Action Properties
- `actionId`
- `params`
- `onSuccess`
- `onError`

Actions must map to **approved action adapters** in the host application.

---

## State & Data Binding

- Form values and outputs can be referenced symbolically
- No direct data fetching logic in SUIP
- Data sources are declared, not executed

---

## Accessibility

SUIP supports:
- ARIA roles
- Labels and hints
- Focus order

Rendering engines are responsible for enforcing platform accessibility standards.

---

## Example SUIP Document

```json
{
  "suipVersion": "0.1",
  "sceneId": "support_start",
  "layout": {
    "type": "stack",
    "children": [
      { "type": "text", "variant": "h2", "content": "How can we help?" },
      {
        "type": "button",
        "label": "Pay my bill",
        "action": {
          "actionId": "billing.startPayment"
        }
      }
    ]
  },
  "chat": {
    "persistent": true,
    "suggestedPrompts": ["I need help with a product", "Show my invoices"]
  }
}
```

---

## Rendering Engines

A rendering engine:
- Validates SUIP schema
- Maps components to native UI
- Executes actions via adapters
- Preserves design system constraints

Supported platforms:
- Web
- iOS
- Android
- Desktop
- Voice

---

## Versioning & Compatibility

- Backward‑compatible minor versions
- Breaking changes require major version bump

---

## Summary

SUIP enables **conversationally compiled interfaces** by separating:
- *Intent planning* (LLM)
- *Experience description* (SUIP)
- *Execution & rendering* (platform runtimes)

This makes UI portable, adaptive, and dramatically cheaper to build and maintain.

