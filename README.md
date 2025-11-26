<p align="center">
  <img src="https://img.shields.io/badge/Protocol-IoTIVP%20Core-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Stable-green?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Model-JSON%20Schema-lightgrey?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Hash-Pipeline%201.5-yellow?style=for-the-badge"/>
</p>

# 🧠 IoTIVP-Core v1.5  
### **Structured JSON + Deterministic Hashing**

IoTIVP-Core translates raw IoTIVP-Binary packets into a standardized JSON format.  
It defines the **trusted sensor packet schema** shared across the ecosystem.

---

# 🧩 Core JSON Structure

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

# 🔐 Deterministic Hashing Pipeline (v1.5)

```
hash_input =
    header
  + timestamp
  + device_id
  + sorted(fields)
  + nonce
  + secret
```

Hash output: **4–8 bytes**, BLAKE2s recommended.

---

# 🔧 Python Example: Compute Hash

```python
from iotivp_core_hash import compute_core_hash

hash_hex = compute_core_hash(
    packet_json,
    secret=b"demo-secret",
    hash_len=4
)

print("hash:", hash_hex)
```

---

# 📘 Features  
- ✔ Stable cross-system JSON representation  
- ✔ Deterministic hashing  
- ✔ TLV → field mapping rules  
- ✔ Ideal for cloud, dashboards, robotics  

---

# 🧱 Ideal For  
- AI/ML robotics decision loops  
- Cloud aggregators (AWS/GCP/CF Workers)  
- Sensor dashboards  
- n8n automations  

---

**IoTIVP-Core is the “source of truth” before verification.**
