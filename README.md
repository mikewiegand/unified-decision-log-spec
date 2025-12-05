# Unified Decision Log Specification (UDLS)

UDLS is a lightweight, replayable, governance-aligned decision logging standard for IoT devices, autonomous agents, orchestration systems, and offline-first environments.

Traditional logs answer “what happened?”  
UDLS answers **“what was decided, why, and with what evidence?”**

UDLS gives devices and agents a shared language of:
- **Intents**
- **Actions**
- **Rationales**
- **Inputs & Context**
- **Results**
- **Telemetry (drift, scores, model lineage)**
- **Integrity metadata**

## Why UDLS?

Modern systems—from ESP32 nodes to server-side policy engines—make autonomous decisions constantly, but rarely expose:

- why they acted  
- what inputs they considered  
- what internal checks passed or failed  
- how model updates or sensor drift changed behavior  
- how trustworthy the device’s timeline was  

UDLS brings **transparency, replayability, governance, and safety** to these systems.

### Key Properties

- Deterministic replay  
- Low-overhead encoding for MCU devices  
- Trust-aware timestamps  
- LLM/Model lineage support  
- Offline & intermittent operation  
- Distributed ordering compatibility  

## Core Concepts (high‑level)

### Temporal Ordering
UDLS supports:
- **monotonic counters**
- **Lamport logical clocks**
- **hybrid logical clocks**

### Timestamp Trust Model
Every timestamp includes a `source`:
- `ntp`, `gateway`, `rtc`, `server`, or `unknown`

### Model Lineage & Drift Governance
Optional `model` block enables:
- model version tracking
- drift detection
- eval-based rollouts  
- deterministic replay of AI-driven decisions

### ESP32 Time Authority Integration
UDLS supports deployments with a local LAN time master (ESP32-based), enabling:
- reliable temporal alignment
- drift scoring
- replay safety even in offline clusters

## Example (MCU-friendly)

```json
{
  "v": 3,
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
```

## Reference Implementation Notes
- Supports JSON Lines (`.jl` / `.jsonl`)  
- Ring-buffer or append-only local storage  
- Works over MQTT, HTTP, BLE, ESP‑NOW  

## Roadmap
- v3.2 — richer model-eval schema  
- v4 — streaming UDLS  
- v5 — OpenTelemetry compatibility layer  
