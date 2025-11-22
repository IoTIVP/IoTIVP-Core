# IoTIVP-Core v1.5 – Specification (Draft)

**Layer:** Human-readable integrity verification for IoT, robotics, and low-power systems  
**Author:** PD2KC.ai — IoTIVP Protocol Team  
**Status:** Draft / Experimental

---

## 1. Purpose

IoTIVP-Core v1.5 defines a **structured, human-readable format** for verifying the integrity of sensor and telemetry data before it is:

- Acted on by robots, gateways, or services  
- Forwarded into analytics pipelines  
- Encoded into compact wire formats (e.g., IoTIVP-Binary v1.0)  

Goals:

- Make integrity checks **visible and explainable**
- Provide a consistent structure across devices and vendors
- Allow **field-level** verification, not just blind packet checks
- Be simple enough for microservices, gateways, and n8n-style workflows

---

## 2. Core Data Model

A basic IoTIVP-Core packet is a JSON object:

```json
{
  "version": "1.5",
  "device_id": "DEV-1234",
  "timestamp": 1737591022,
  "readings": {
    "temp": 22.4,
    "hum": 54.8,
    "batt": 3.74
  },
  "nonce": 117,
  "hash": "6ac4fa2e45f1478f"
}
```

### 2.1. Required Fields

| Field       | Type         | Required | Description                                      |
|------------|--------------|----------|--------------------------------------------------|
| `version`  | String       | Yes      | Protocol version (e.g., `"1.5"`)                |
| `device_id`| String       | Yes      | Unique or profile-defined device identifier     |
| `timestamp`| Integer      | Yes      | UNIX epoch seconds or agreed device time base   |
| `readings` | Object       | Yes      | Key/value telemetry (profile-dependent)         |
| `hash`     | String       | Yes      | Integrity digest (hex)                          |

### 2.2. Optional Fields

| Field    | Type    | Required | Description                           |
|---------|---------|----------|---------------------------------------|
| `nonce` | Integer | No       | Replay protection counter / token    |
| `meta`  | Object  | No       | Additional metadata (profile-specific) |

---

## 3. Hashing Rules (Field-Level Integrity)

IoTIVP-Core hashes **content fields**, not just bytes on the wire.

Default recommended algorithm: **BLAKE2s-256 → truncated**  
Alternatives (deployment choice):

- BLAKE2s  
- SHA-256  
- xxHash (for low-risk internal uses)

### 3.1. Hash Input Canonicalization

Fields included in the hash (in order):

1. `version`
2. `device_id`
3. `timestamp`
4. `readings` (serialized deterministically)
5. `nonce` (if present)

Implementations MUST:

- Use a consistent JSON serialization or canonical string format  
- Maintain field order in the hash input  
- Ensure numeric fields are not stringified inconsistently

Example conceptual hash input string:

```text
version=1.5|device_id=DEV-1234|timestamp=1737591022|readings={"temp":22.4,"hum":54.8,"batt":3.74}|nonce=117
```

*(Exact format is defined per deployment profile, but MUST be consistent on both sender and receiver.)*

### 3.2. Hash Output

- Represented as **lowercase hex string**
- May be truncated (e.g., 8 bytes → 16 hex chars)
- Length agreed by deployment profile (e.g., 8, 16, 32 bytes)

---

## 4. Nonce & Replay Protection

`nonce` is optional but **strongly recommended** in shared or untrusted channels.

Rules:

- Monotonically increasing, or  
- Pseudo-random with collision-resistance

Receivers should:

- Track recent nonces per `device_id`
- Reject:
  - Duplicate nonces within a sliding window
  - Nonces that move backwards (for monotonic mode)

---

## 5. Device Identification & Fingerprinting

`device_id` may be:

- A direct ID (e.g., `"DEV-1234"`)
- A hash of:
  - MCU unique ID
  - MAC address
  - Serial number

Best practice:

- Do **not** expose raw hardware IDs openly
- Use stable, truncated hashes instead

This allows:

- Device grouping and policy assignment
- Mapping to IoTIVP-Binary device IDs
- Forensic tracking without leaking raw identifiers

---

## 6. Validation Procedure (Receiver)

When a receiver processes an IoTIVP-Core packet:

1. Check that all **required fields** exist.  
2. Verify `version` is one that the receiver supports.  
3. Validate `timestamp` (e.g., within allowed drift window).  
4. If `nonce` is present:
   - Verify monotonicity or uniqueness  
5. Reconstruct the hash input based on:
   - Agreed canonical format  
   - Profile rules  
6. Compute hash using the deployment’s selected algorithm.  
7. Compare computed hash to the `hash` field.  
8. If any step fails → **reject packet** and mark as invalid.

---

## 7. Profiles (v1.5 Concept)

IoTIVP-Core v1.5 introduces the idea of **profiles**:  
Predefined sets of fields and rules tailored to specific device classes.

### 7.1. Profile A – Environmental Sensors

Example `readings` object:

```json
"readings": {
  "temp": 22.4,
  "hum": 54.8,
  "pressure": 1012.3,
  "batt": 3.74
}
```

Constraints (example):

- `temp`: -40.0 to +85.0  
- `hum`: 0.0 to 100.0  
- `pressure`: 800–1200 hPa  
- `batt`: 2.5–4.5 V  

### 7.2. Profile B – Robotics Telemetry

```json
"readings": {
  "motor_rpm": 1450,
  "imu_pitch": -3.4,
  "imu_roll": 1.2,
  "imu_yaw": 182.7,
  "batt": 23.4,
  "temp": 48.7
}
```

Profiles may define:

- Which fields are mandatory
- Allowed value ranges
- Units
- Hash truncation length
- Recommended validation policies

*(A full profile catalog can be maintained in `/docs/reference/`.)*

---

## 8. Relationship to IoTIVP-Binary

IoTIVP-Core is **not** a wire format — it’s a **semantic integrity layer**.

Usage patterns:

- Edge device produces IoTIVP-Core packet →  
  Gateway validates → encodes into IoTIVP-Binary for LoRa/BLE transmission.

- Gateway receives IoTIVP-Binary payload →  
  Decodes → reconstructs/validates against IoTIVP-Core rules for logging and analysis.

**Core provides meaning and policy.  
Binary provides compression and efficient transport.**

---

## 9. Error Handling & Logging

Receivers should:

- Log invalid packets with:
  - `device_id`
  - Reason for failure (hash mismatch, bad timestamp, invalid field, etc.)
- Optionally track simple device risk scores:
  - Too many invalid packets → flag device for investigation

---

## 10. Versioning

This document covers: **IoTIVP-Core v1.5 (Draft)**

Planned evolutions:

- v1.6 – Signed hashes (MAC / key-based)  
- v1.7 – Field anomaly scoring (simple heuristic rules)  
- v2.0 – Superposition Mode (IoTIVP-S) & dual-payload semantics  

---

## 11. Security Notes

- IoTIVP-Core by itself is **integrity-focused**, not full encryption.  
- For confidentiality, use:
  - Encrypted channels (TLS, DTLS, VPNs)  
  - Encrypted payloads beneath/around IoTIVP structures  

IoTIVP-Core helps answer:  
> “Can I trust that this data came from the right device and wasn’t tampered with?”

---

## 12. PD2KC.ai Signature

**Assume nothing. Verify everything. Catch the corbeaux alive.**
