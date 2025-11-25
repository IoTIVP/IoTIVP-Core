# IoTIVP-Core v1.5  
**Internet of Things Integrity Verification Protocol — Core Layer**

IoTIVP-Core defines the **structured, human-readable JSON representation** of IoT telemetry before it is compressed into IoTIVP-Binary v1.0 or after Binary packets are decoded by a gateway.

IoTIVP-Core is used by:

- Gateways  
- Servers  
- n8n workflows  
- Analytics dashboards  
- Robotics & simulation systems  

It is the **logic + integrity engine** of the IoTIVP ecosystem.

---

# 1. Role of IoTIVP-Core

IoTIVP-Core sits directly above IoTIVP-Binary and directly below IoTIVP Verify Engine:

```
+--------------------------------------------------------+
|                 IoTIVP Ecosystem Layers                |
+--------------------------------------------------------+
|  Dashboards / Analytics / Robotics Control Systems     |
|  IoTIVP Verify Engine (Integrity Score v2.0)           |
|  IoTIVP-Core (Structured JSON Protocol v1.5)           |
|  IoTIVP-Binary v1.0 (Binary Wire Packet Format)        |
|  Device Firmware (Sensors, ESP32, LoRa, BLE, Robots)   |
+--------------------------------------------------------+
```

**Purpose:**  
IoTIVP-Core describes fields, timestamp rules, hashing rules, and required metadata *before encoding* (or after decoding from Binary).

---

# 2. IoTIVP-Core JSON Structure

A valid IoTIVP-Core packet looks like:

```json
{
  "header": 1,
  "timestamp": 1732212000,
  "device_id": 42,
  "nonce": 56,
  "fields": {
    "temperature": 23.5,
    "humidity": 60,
    "battery": 92
  },
  "hash": "a9f23d91"
}
```

### Required Fields
| Key | Description |
|-----|-------------|
| **header** | version/flags for IoTIVP-Core |
| **timestamp** | seconds or ms since epoch |
| **device_id** | unique device identifier |
| **fields** | dictionary of actual sensor readings |
| **hash** | truncated digest over fields |
| **nonce** | optional replay-protection value |

---

# 3. Integrity Hash Pipeline (Core → v1.5)

IoTIVP-Core v1.5 uses a clean, deterministic hash computation:

```
hash_input =
    header +
    timestamp +
    device_id +
    ordered(fields) +
    nonce (optional) +
    shared_secret
```

Hash algorithm (recommended):  
**BLAKE2s**, truncated to **4–8 bytes**.  
SHA-256 truncated is also supported.

**Important:**  
Fields must be hashed in **sorted key order**:

```json
{
  "battery": 92,
  "humidity": 60,
  "temperature": 23.5
}
```

This ensures cross-language determinism.

---

# 4. Integrity Score v2.0 (Verification Layer)

IoTIVP-Core feeds directly into the IoTIVP Verify Engine.

The Verify Engine generates a trust score from **0–100** across five dimensions:

| Dimension | Weight | Description |
|----------|--------|-------------|
| **Hash Validity** | 40% | Was the hash correct? |
| **Timestamp Validity** | 20% | Within allowed window? |
| **Sequence / Nonce Behavior** | 15% | Replay or anomalies? |
| **Signal/Value Anomalies** | 15% | Out-of-range or unrealistic values? |
| **Device Behavior Profile** | 10% | Expected frequency, battery curves, etc. |

Output example:

```json
{
  "valid": true,
  "integrity_score": 94,
  "details": {
    "hash_valid": true,
    "timestamp_fresh": true,
    "nonce_behavior": "clean",
    "anomalies": []
  }
}
```

---

# 5. Mapping IoTIVP-Core ↔ IoTIVP-Binary

All IoTIVP-Core packets can be converted to IoTIVP-Binary v1.0 via TLV fields.

### Example Mapping

| Core JSON Field | TLV Type ID | TLV Value |
|-----------------|-------------|-----------|
| `temperature` | `0x01` | fixed-point int (x10) |
| `humidity` | `0x02` | 1-byte uint |
| `battery` | `0x03` | 1-byte uint |
| `custom.*` | `0x10–0xFF` | variable |

A gateway performs:

- **Core → Binary**  
- **Binary → Core**  

using the same field catalog.

---

# 6. IoTIVP-Core Field Catalog (v1.5)

### Standard Environment Fields
| Name | TLV ID | Type |
|------|--------|------|
| `temperature` | 0x01 | int16 (fixed point, x10) |
| `humidity` | 0x02 | uint8 |
| `battery` | 0x03 | uint8 |
| `pressure` | 0x04 | 2–4 bytes |
| `light` | 0x05 | uint16 |

### Robotics / Motion Fields
| Name | TLV ID | Type |
|------|--------|------|
| `accel_x` | 0x10 | int16 |
| `accel_y` | 0x11 | int16 |
| `accel_z` | 0x12 | int16 |
| `gyro_x` | 0x13 | int16 |
| `gyro_y` | 0x14 | int16 |
| `gyro_z` | 0x15 | int16 |

### Custom / Reserved
- `0x80–0xFF` reserved for future modules.

---

# 7. Reference Hash Function (Python)

Add this file:

`iotivp_core_hash.py`

```python
import hashlib
import json

def compute_core_hash(packet: dict, secret: bytes, hash_len=4):
    """
    Compute IoTIVP-Core hash (v1.5).
    Deterministic field ordering.
    """
    header = packet["header"]
    timestamp = packet["timestamp"]
    device_id = packet["device_id"]
    nonce = packet.get("nonce")

    fields = packet["fields"]
    sorted_items = sorted(fields.items(), key=lambda x: x[0])

    hash_input = (
        str(header) +
        str(timestamp) +
        str(device_id)
    )

    for k, v in sorted_items:
        hash_input += f"{k}:{v}"

    if nonce is not None:
        hash_input += str(nonce)

    hash_input += secret.decode()

    digest = hashlib.blake2s(hash_input.encode()).digest()
    return digest[:hash_len].hex()
```

---

# 8. Example IoTIVP-Core Packet (Sensor)

```json
{
  "header": 1,
  "timestamp": 1732212000,
  "device_id": 42,
  "nonce": 77,
  "fields": {
    "temperature": 23.5,
    "humidity": 60,
    "battery": 91
  },
  "hash": "baf24977"
}
```

---

# 9. Example IoTIVP-Core Packet (Robotics)

```json
{
  "header": 1,
  "timestamp": 1732212022,
  "device_id": 71,
  "fields": {
    "accel_x": 121,
    "accel_y": -5,
    "accel_z": 0,
    "gyro_x": 3,
    "gyro_y": -2,
    "gyro_z": 1
  },
  "hash": "93aa21d7"
}
```

---

# 10. Workflow Summary

1. **Device** produces field values.  
2. **Firmware** builds IoTIVP-Core JSON.  
3. JSON is **hashed** using Core rules.  
4. JSON is **encoded** into IoTIVP-Binary TLV.  
5. Device transmits Binary.  
6. **Gateway decodes** Binary → Core JSON.  
7. **Verify Engine** calculates Integrity Score v2.0.  
8. Clean data flows into **n8n**, analytics, ML, robotics.

---

# 11. Versioning

- IoTIVP-Core v1.5 — current  
- IoTIVP-Binary v1.0 — binary layer  
- IoTIVP Verify Engine v1.5 — scoring layer  
- IoTIVP Ecosystem v1 — combined suite  

---

IoTIVP-Core is now a **complete, ready-to-publish standard**  
for IoT, robotics, and low-power networks.
