# IoTIVP – CHANGELOG

This document tracks official protocol updates across both IoTIVP-Core and IoTIVP-Binary.

---

## [1.5.0] — 2025-11-21
### IoTIVP-Core v1.5 Released
- Introduced structured human-readable integrity format  
- Added field-level hashing rules  
- Added device fingerprinting guidance  
- Added nonce and replay-protection model  
- Added profile system (Environmental, Robotics, BLE)  
- Standardized canonical hash input ordering  
- Added full validation procedure  
- Added timestamp drift guidance  
- Added Core → Binary interoperability model  
- Added v1.6 and v2.0 roadmap  

---

## [1.0.0] — 2025-11-21
### IoTIVP-Binary v1.0 Released
- Defined base binary wire format  
- Added header bitfield layout  
- Added timestamp (2–4 byte) rules  
- Added device ID (1–4 byte) formats  
- Added TLV and packed-field data blocks  
- Added optional Nonce field for anti-replay  
- Added hash field (xxHash32/64, SipHash, BLAKE2s)  
- Added LoRa/BLE/Robotics examples  
- Added test vector structure  
- Added Binary → Core flow  
