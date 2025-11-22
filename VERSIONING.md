# IoTIVP – Versioning Rules

This document describes how versions are defined and incremented for the IoT Integrity Verification Protocol (IoTIVP), including both:

- **IoTIVP-Core** (human-readable integrity format)  
- **IoTIVP-Binary** (byte-level encoding format)  

---

# 🔹 1. Version Format

IoTIVP follows **Semantic Versioning (SemVer-inspired)** with adaptations for protocol specifications.

```
MAJOR.MINOR.PATCH
```

### ▶ MAJOR  
Breaking or incompatible changes:  
- Changing field names  
- Modifying hash order  
- Changing binary layout  
- Removing fields  
- New modes that invalidate old parsers  

### ▶ MINOR  
Backwards-compatible enhancements:  
- New optional fields  
- New profiles  
- Additional hash algorithms  
- New recommended validation rules  

### ▶ PATCH  
Trivial fixes:  
- Typos  
- Documentation improvements  
- Clarifications  

---

# 🔹 2. Versioning Principles

### 2.1. No Silent Breaks  
Any change that disrupts existing implementations MUST increment **MAJOR**.

### 2.2. Profiles Are Additive  
Adding new profiles does NOT break existing devices → **MINOR**.

### 2.3. Hash Algorithm Additions  
New algorithms → **MINOR**  
Changing defaults → **MAJOR**

### 2.4. IoTIVP-Core and IoTIVP-Binary Are Versioned Independently  
Example:

- IoTIVP-Core: `1.5.0`
- IoTIVP-Binary: `1.0.0`

They evolve at different speeds but remain interoperable.

### 2.5. Superposition Mode (IoTIVP-S)  
This is a separate **MAJOR** feature and will begin at:

```
IoTIVP-S 1.0
```

---

# 🔹 3. Current Versions

| Layer           | Version | Status |
|-----------------|---------|--------|
| IoTIVP-Core     | 1.5.0   | Draft  |
| IoTIVP-Binary   | 1.0.0   | Stable |

---

# 🔹 4. Forward Compatibility Expectations

Receivers SHOULD:
- Ignore unknown optional fields  
- Accept future MINOR fields when possible  
- Reject only when required fields or hashes fail  

Transmitters SHOULD:
- Not rely on receivers understanding future fields  

---

# 🔹 5. When In Doubt  
Any change that could confuse a parser → **MAJOR** bump.

---

**PD2KC.ai — Assume nothing. Verify everything.**  
