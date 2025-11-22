# IoTIVP – Protocol Roadmap  
**Maintained by PD2KC.ai — IoTIVP Protocol Team**

This is the official multi-layer roadmap guiding IoTIVP-Core, IoTIVP-Binary, and future IoTIVP-S development.

---

# 🔹 Phase 1 — Foundation (Completed)

### ✔ IoTIVP-Binary v1.0  
- Header bitfields  
- TLV / Packed blocks  
- Hash field  
- Optional nonce  
- LoRa/BLE/Robotics examples  
- Test structure

### ✔ IoTIVP-Core v1.5  
- Human-readable verification  
- Field-level hashing  
- Profiles (Env, Robotics, BLE)  
- Canonical hashing order  
- Replay protection  
- Validation flow  

---

# 🔹 Phase 2 — Expansion (In Progress)

### 🔸 IoTIVP-Binary v1.1
- Delta encoding  
- Compressed TLV  
- Deterministic packed profiles  

### 🔸 IoTIVP-Core v1.6
- Signed hashes (MAC / shared key)  
- Device trust scoring  
- Field anomaly detection (range + drift)  

---

# 🔹 Phase 3 — Advanced Security (Planned)

### 🔸 IoTIVP-Superposition (IoTIVP-S) v1.0
A dual-payload, dual-validation model inspired by quantum superposition concepts:

- Two partial states hashed independently  
- Combined superstate integrity check  
- Selective reveal for gateways  
- Multi-layer trust reconstruction  
- Designed for robotics, safety-critical IoT  

### 🔸 IoTIVP-Binary v1.2
- Two-segment binary packets  
- Interleaved digest protection  
- Superposition-aware encode paths  

---

# 🔹 Phase 4 — Ecosystem & Tooling

### 🔸 Open-Source Reference Library
- Python  
- Rust  
- Embedded C  
- TypeScript  

### 🔸 Test Vectors & Fuzzing Suite
- Randomized invalid packets  
- Hash mismatch fuzzing  
- Range anomaly fuzzing  

### 🔸 n8n Integration Node
- Encode/decode  
- Validate  
- Auto-hash  
- Profile enforcement  
- Connect Core ➝ Binary ➝ LoRa / BLE  

---

# 🔹 Phase 5 — Long-Term Vision

### 🔸 IoTIVP 2.0
- Full ecosystem alignment  
- Mandatory MAC mode  
- Formal verification  
- Signed profile manifests  
- Hardware-backed authentication (TPM, Secure Element)  

### 🔸 IoTIVP-S 2.0
- Multi-superstate fusion  
- Probabilistic trust scoring  
- Redundant state reconstruction  

---

**Assume nothing. Verify everything. Catch the corbeaux alive.**  
