# State and Binding Semantics (SUIP v0.1)

## Purpose

This document defines how **state** is represented and how **bindings** (e.g. `{{payForm.amount}}`) are resolved in SUIP-driven UIs.

The goal is to keep bindings:
- Deterministic
- Easy to implement
- Safe for LLM generation

---

## State Model

Rendering engines maintain a **session-scoped state object**.

### Canonical Shape

```json
{
  "forms": {
    "<formId>": {
      "<fieldId>": <value>
    }
  },
  "flags": {
    "<string>": <boolean>
  },
  "data": {
    "<string>": <object | array | primitive>
  }
}
```

### Rules
- State is **owned by the renderer**, not the LLM
- LLM can *reference* state but cannot mutate it directly
- State mutations occur only via:
  - form input changes
  - `setState` action outcomes

---

## Bindings

Bindings are symbolic references to state values.

### Syntax

```
{{path.to.value}}
```

### Examples

- `{{payForm.amount}}`
- `{{forms.payForm.invoiceNumber}}`
- `{{data.selectedProduct.id}}`

### Resolution Rules

1. Bindings are resolved **at action execution time**
2. Unresolved bindings:
   - Cause action execution to fail
   - Trigger `onError` if defined
3. Bindings are **not evaluated** in text content for v0.1

---

## Allowed Binding Locations (v0.1)

✅ Action parameters (`action.params`)

🚫 Layout decisions
🚫 Component visibility logic
🚫 Arbitrary expressions

---

## Future Extensions (Out of Scope)

- Computed bindings
- Transformation functions
- Two-way bindings outside forms

---

## Summary

State and bindings in v0.1 are intentionally limited to ensure:
- Predictable rendering
- Easy validation
- Low implementation complexity

