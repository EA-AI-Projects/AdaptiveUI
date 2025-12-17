# Action Runtime Contract (SUIP v0.1)

## Purpose

This document defines how **actions** declared in SUIP are executed at runtime and how results are handled.

---

## Action Lifecycle

1. User interacts with a component (e.g. button)
2. Renderer resolves bindings in `action.params`
3. Renderer sends action request to host/orchestrator
4. Host executes the action adapter
5. Host returns an action result
6. Renderer applies `onSuccess` or `onError`

---

## Action Request Shape

```json
{
  "actionId": "billing.submitPayment",
  "params": {
    "invoiceNumber": "INV-1042",
    "amount": 120.5
  }
}
```

---

## Action Response Shape

```json
{
  "status": "ok",
  "data": { "receiptId": "R-9012" },
  "outcome": {
    "type": "navigate",
    "targetSceneId": "billing_success"
  }
}
```

### Fields

- `status`: `ok | error`
- `data`: optional payload
- `outcome`: optional override of SUIP-defined outcomes

---

## Outcome Resolution Rules

1. If response includes `outcome`, renderer MUST use it
2. Otherwise:
   - `status == ok` → apply `onSuccess`
   - `status == error` → apply `onError`

---

## Security Rules

- Only allowlisted actions may be executed
- Parameters must validate against manifest schema
- Renderers must never execute arbitrary code

---

## Summary

Actions are the **only bridge** between declarative UI intent and imperative business logic, and are strictly controlled by the manifest.

