# **IoTIVP-Core v1.5**  
### *Human-Readable Integrity Verification Protocol for IoT, Robotics & Low-Power Systems*  
**By PD2KC.ai — IoTIVP Protocol Team**

![Version](https://img.shields.io/badge/IoTIVP--Core-v1.5.0-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Draft-orange?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Spec Type](https://img.shields.io/badge/Spec-Human%20Readable-lightgrey?style=for-the-badge)
![Maintainer](https://img.shields.io/badge/PD2KC.ai-IoTIVP%20Protocol%20Team-purple?style=for-the-badge)


IoTIVP-Core v1.5 is the **human-readable, structured, explainable layer** of the IoTIVP ecosystem.  
It defines a clear, flexible, machine-verifiable data format for IoT, robotics, sensors, LoRa, BLE, mesh devices, and low-power embedded systems.

This layer focuses on:
- **Transparency**
- **Field-level hashing**
- **Normalization**
- **Replay protection**
- **Lightweight device fingerprinting**
- **Gateway-friendly validation**

IoTIVP-Core sits **above** IoTIVP-Binary v1.0.  
Together, they form a complete integrity verification protocol stack.

---

# 🔹 **Core Mission**
IoTIVP-Core provides a framework for:
- Validating sensor data  
- Detecting tampering  
- Ensuring source authenticity  
- Normalizing unpredictable IoT payloads  
- Preparing clean data before encoding into Binary v1.0

Think of IoTIVP-Core as the **blueprint** and IoTIVP-Binary as the **ammunition**.

---

# 🔹 **1. Structure Overview**

IoTIVP-Core packets are human-readable and follow a predictable, standard format.

```
{
  "version": "1.5",
  "device_id": "...",
  "timestamp": "...",
  "readings": {
      "temp": 23.1,
      "hum": 55.4
  },
  "nonce": 91,
  "hash": "..."
}
```

Core fields:

| Field | Required | Purpose |
|-------|----------|----------|
| **version** | Yes | Protocol version (e.g., 1.5) |
| **device_id** | Yes | Unique per-device identifier |
| **timestamp** | Yes | UNIX epoch or device-timer |
| **readings** | Yes | Sensor/telemetry values |
| **nonce** | Optional | Replay prevention |
| **hash** | Yes | Field-level integrity verification |

---

# 🔹 **2. Hashing (Field-Level Integrity)**

IoTIVP-Core hashes individual fields — not just the entire packet.

Default algorithm: **BLAKE2s-256 → truncated**  
Allowed algorithms:
- BLAKE2s  
- SHA-256  
- xxHash (low-risk scenarios)

Hash covers:

```
version + device_id + timestamp + readings + nonce
```

The final `hash` field is included after computation.

---

# 🔹 **3. Device Fingerprinting (Optional)**

Devices may include:
- MCU unique ID hash  
- MAC address hash  
- Serial number hash  

These are hashed before inclusion to avoid leaking raw identifiers.

This supports:
- Authenticity  
- Mapping devices to trust groups  
- Cross-validation with IoTIVP-Binary device IDs  

---

# 🔹 **4. Nonce & Replay Protection**

The **nonce** is optional but recommended.

Rules:
- 1–4 bytes  
- Monotonic counter or pseudo-random  
- Receiver remembers last-seen nonces  
- Reject on duplicates

This protects:
- Shared LoRa channels  
- BLE broadcast environments  
- Swarm robotics chatter  

---

# 🔹 **5. Validation Flow (Core Layer)**

Receiver performs:

1. Validate JSON structure  
2. Recompute field-level hash  
3. Compare to `hash` field  
4. Validate timestamp drift  
5. Check nonce (if supplied)  
6. Pass structured data to Binary encoder or analytics layer  

If any step fails → **reject packet**.

---

# 🔹 **6. Profiles (v1.5 feature)**

IoTIVP-Core supports **profiles** for different application types:

### ✔️ Environmental Sensors (Profile A)
- temp  
- hum  
- pressure  
- battery  

### ✔️ Robotics Telemetry (Profile B)
- motor RPM  
- IMU  
- pose  
- battery/thermal  

### ✔️ BLE Nearby Devices (Profile C)
- proximity  
- short sensor readings  
- device state  

Profiles define:
- Required fields  
- Acceptable ranges  
- Field hashing order  
- Validation rules  

---

# 🔹 **7. Core → Binary Relationship**

| Layer | Purpose |
|--------|---------|
| **Core (v1.5)** | Human-readable, explainable, profile-based integrity |
| **Binary (v1.0)** | Compact, byte-level encoding for constrained networks |

Core packets may be:

- Used standalone  
- Passed into IoTIVP-Binary encoders  
- Validated at gateways before encoding  
- Logged for debugging  
- Stored for auditing  

**Core gives meaning.**  
**Binary gives efficiency.**

---

# 🔹 **8. Example IoTIVP-Core Packet**

```
{
  "version": "1.5",
  "device_id": "DEV-A19F",
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

---

# 🔹 **9. Roadmap**

### **v1.5 → v1.6**
- Signed hashes (MAC mode)  
- Device trust scoring  
- Field-range anomaly detection  

### **v1.6 → v2.0**
- Superposition Mode (IoTIVP-S)  
- Dual-payload hashing  
- Verified synthetic readings  

---

# 🔹 **10. Repo Structure**

```
docs/
  specs/
    v1.4/
    v1.5/
  diagrams/
  reference/
examples/
tests/
```

---

# 🔹 PD2KC.ai Signature Line  
**Assume nothing. Verify everything. Catch the corbeaux alive.**

---
