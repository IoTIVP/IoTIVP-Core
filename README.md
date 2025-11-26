<p align="center">
  <img src="https://img.shields.io/badge/Protocol-IoTIVP%20Core-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Stable-green?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Model-JSON%20Schema-lightgrey?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Hash-Pipeline%201.5-yellow?style=for-the-badge"/>
</p>

# 🧠 IoTIVP-Core v1.5

### **Structured JSON + Deterministic Hashing**

IoTIVP-Core is the **human-readable** form of sensor data in the IoTIVP ecosystem.  
It acts as the bridge between raw binary packets and integrity verification.

---

# 📄 Core JSON Structure

```json
{
  "header": 1,
  "timestamp": 1732212000,
  "device_id": 42,
  "nonce": 7,
  "fields": {
    "temperature": 23.5,
    "humidity": 60,
    "battery": 91
  },
  "hash": "baf24977"
}
```

---

# 🔐 Hashing Pipeline (Deterministic)

The hash input is:

```
header + timestamp + device_id + sorted(fields) + nonce + secret
```

Then hashed via:

- `blake2s` (default)  
- or `sha256`  

Output is truncated to **4–8 bytes**, depending on the profile.

---

# 🎯 What IoTIVP-Core Provides

- ✔ Stable cross-system data format  
- ✔ Source-ordered hashing (prevents manipulation)  
- ✔ Works with gateways, cloud, robotics, n8n  
- ✔ Maps directly from Binary → Core via TLV  

---

# 🔧 Example: Compute Hash

```python
from iotivp_core_hash import compute_core_hash

hash_hex = compute_core_hash(packet, secret=b"demo-secret", hash_len=4)
print(hash_hex)
```

---

# 📚 Core Concepts

- **fields{}** — arbitrary sensor or robotics data  
- **nonce** — replay protection  
- **hash** — integrity + tamper detection  
- **device_id** — unsigned, consistent identity  

---

# 🔐 Why IoTIVP-Core?

It creates a **stable, trusted, predictable** data structure that all downstream systems can rely on.

IoTIVP-Core is the **source of truth** before verification.

