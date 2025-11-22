# Contributing to IoTIVP

Thank you for your interest in contributing to the **IoT Integrity Verification Protocol (IoTIVP)**.  
This is an open protocol effort under **PD2KC.ai — IoTIVP Protocol Team**.

We welcome:

- Bug reports  
- Security concerns  
- Profile proposals  
- New examples  
- Implementations (Python, C, Rust, embedded, etc.)  
- Documentation improvements  
- Research and whitepapers  

---

# 🔹 1. Code of Conduct

All contributors must follow respectful, professional communication.  
No harassment, aggression, or disrespectful behavior.

---

# 🔹 2. How to Contribute

### 2.1. Reporting Issues
Open an issue in GitHub with:

- Clear description  
- Affected version (Core or Binary)  
- Reproduction steps  
- Expected vs actual behavior  

### 2.2. Adding Profiles
Submit a PR with:

- Profile name  
- Required readings  
- Optional readings  
- Validation rules  
- Hashing rules (if custom)  
- Examples  

### 2.3. Submitting Implementations
You may contribute:

- Parsers  
- Encoders  
- Decoders  
- Hash utilities  
- Test vectors  

Recommended languages:

- Python  
- C / C++  
- Rust  
- Go  
- TypeScript  
- Embedded C (ESP32, STM32, AVR)  

---

# 🔹 3. Specification Contributions

Specs live under:

```
docs/specs/v1.x/
```

Changes to specs require:

- Clear explanation  
- Version bump (MAJOR/MINOR/PATCH)  
- Updated CHANGELOG.md  
- Test vectors where applicable  

---

# 🔹 4. Security Reporting

If you find a vulnerability, please open a **private GitHub security advisory**, not a public issue.

We will review and coordinate disclosure.

---

# 🔹 5. Style Guidelines

### For Core (JSON):
- Use lowercase field names  
- Use consistent numeric formatting  
- Deterministic key ordering in examples  

### For Binary:
- Use uppercase hex (A–F)  
- Group bytes visually for readability  
- Annotate sample packets  

---

# 🔹 6. License  
Contributions are accepted under the **MIT License**.

---

**PD2KC.ai — Catch the corbeaux alive.**  
