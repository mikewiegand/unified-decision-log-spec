# Unified Decision Log Specification (UDLS)

UDLS is a lightweight, replayable, and governance-aligned decision logging
standard for IoT devices, autonomous agents, orchestration systems, and
offline-first environments.

Traditional logs answer *“what happened?”*  
UDLS answers **“what was decided, why, and with what evidence?”**

It gives devices and agents a shared language of:
- **Intents**
- **Actions**
- **Rationales**
- **Context**
- **Results**
- **Telemetry**
- **Error and drift conditions**

This repository provides the complete public specification for UDLS,
including schema definitions, field semantics, conformance rules, and
reference examples for microcontroller, edge, and server-class systems.

---

## Why UDLS?

Modern systems make autonomous decisions — but they rarely expose:
- *why* they acted  
- *what inputs* they used  
- *what alternatives* were considered  
- *what internal checks* passed/failed  
- *how* the system evaluates drift or error  

UDLS brings **transparency, replayability, governance, and safety** to these
decisions.

Key properties:
- **Deterministic replay**
- **Low-overhead encoding for MCU devices**
- **Alignment with Packweave traits + use-case layer**
- **Queryable by monitoring and analytics systems**
- **Supports offline, intermittent, and delayed transmission**

---

## The UDLS Record Structure

Every UDLS entry contains:

- `ts` — monotonic timestamp  
- `actor` — device or agent identity  
- `intent_id` — which intent the action supports  
- `action` — the operation performed  
- `rationale` — compact explanation  
- `inputs` — relevant conditions, env values, or upstream signals  
- `result` — success/failure and output  
- `ctx` — optional structured metadata  
- `eval` — fields relevant to drift, quality, or tests  
- `sig` — optional cryptographic signature  

UDLS is purposely minimal but expressive enough for replay, audit, and governance.

---

## Example (MCU-friendly)

```json
{
  "v": 2,
  "ts": 1740238403221,
  "actor": "esp32-s3:ntp-master",
  "intent_id": "time_authority.holdover",
  "action": "update_time_health",
  "rationale": "offset=12ms, upstream reachable",
  "inputs": { "offset_ms": 12, "upstream_ok": true },
  "result": "GREEN",
  "eval": { "drift_ms": 12 },
  "ctx": { "poll_interval": 60000 }
}
