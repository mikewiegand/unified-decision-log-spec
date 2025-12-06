# Unified Decision Log Specification (UDLS)
A lightweight, replayable, governance-aligned decision logging standard for IoT devices, autonomous agents, and offline-first systems.

Traditional logs answer “what happened?”
UDLS answers **“what was decided, why, and with what evidence?”**

UDLS gives devices and agents a shared language for:
- intents
- actions
- rationales
- inputs & context
- results
- model lineage & drift telemetry
- integrity metadata
- governed timestamps & ordering

Designed for ESP32-class MCUs, LAN-clustered IoT systems, and agentic LLM orchestration environments.

## Why UDLS?

Modern IoT, AI agents, and distributed control systems constantly make autonomous decisions—but traditional logs hide the most important information.

Most logging frameworks cannot answer:
- why a system chose an action  
- what inputs were used  
- how drift, stale time, or degraded sensors influenced outcomes  
- which ML or LLM model version executed the logic  
- whether checks passed or failed  
- whether the sequence can be deterministically replayed offline

UDLS provides determinism, transparency, replayability, and governance.

## Key Features

### Deterministic Replay
Each UDLS record contains enough structured evidence to replay the decision offline without upstream dependencies.

### Ultra-Low Overhead for MCU Devices
Efficient JSON footprint, append-only ring buffer, monotonic sequencing.

### Timestamp Trust Model
Timestamps include trust source: ntp, gateway, rtc, server, unknown.

### Model Lineage & Drift Governance
Each record may include:
- model version
- drift scores
- eval metrics
- fallback behavior
- lineage tags

### Offline-First Operation
Works even when time drifts or connectivity is intermittent.

## UDLS Decision Record (Developer View)

```json
{
  "v": 3,
  "actor": "esp32-s3:ntp-master",
  "intent_id": "time_authority.holdover",
  "action": "update_time_health",
  "rationale": "offset=12ms, upstream reachable",
  "inputs": { "offset_ms": 12, "upstream_ok": true },
  "result": "GREEN",
  "model": {
    "id": "tiny-ntp-health-eval:v2",
    "drift_ms": 12,
    "confidence": 0.98
  },
  "ts": 1740238403221,
  "ts_src": "ntp",
  "seq": 44221,
  "ctx": { "poll_interval": 60000 }
}
```

## Clock & Ordering Model

Supports:
- monotonic counters  
- Lamport logical clocks  
- Hybrid Logical Clocks (HLC)

### Timestamp Trust
ntp > gateway > server > rtc > unknown

Device must downgrade trust when drift threshold exceeded.

## ESP32 Time Authority Integration

UDLS supports deployments with a local LAN time master (ESP32-based) providing:
- stable LAN time  
- offset measurement  
- drift scoring  
- deterministic replay inside offline clusters  

Example:
```json
{
  "intent_id": "time.sync",
  "action": "apply_ntp_offset",
  "inputs": { "offset_ms": -5 },
  "result": "GREEN"
}
```

## Storage Model

- JSONL  
- append-only ring buffer  
- reboot-safe counters  
- retention optional

## Transmission Model

Supports:
- MQTT  
- HTTP batching  
- BLE  
- ESP-NOW  
- delayed-submit semantics  

## MicroPython Reference Notes

- low heap usage  
- file rotation  
- RTC drift handling  
- monotonic sequence persistence  
- BLE/ESP-NOW–safe JSONL serialization  

## Evaluation Harness

Simulates:
- upstream drift  
- degraded holdover  
- fallback transitions  
- replay-fast-forward  
- model drift scenario playback  

## Roadmap

- v3.2: richer model-eval schema  
- v4: streaming UDLS  
- v5: OpenTelemetry compatibility layer  
