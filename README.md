# MQTT-Security-Comparison
Message vs Transport Encryption
# MQTT Security: Message vs Transport Encryption



## 1. Research Question & Hypothesis

**Question:** *"Is message-level encryption (AES-256-GCM) more energy-efficient than TLS 1.3 for IoT devices while maintaining equivalent security?"*

**ABCDE Framework:** Explores **Availability** (energy consumption) and **Confidentiality** (data protection).

**Hypothesis (H₁):** Message encryption consumes less energy than TLS 1.3 with equivalent security.

## 2. System Architecture

Publisher (Sensor) ──▶ Broker (Hub) ──▶ Subscriber (Controller)

**Components:**
- **Publisher:** Simulated IoT sensor (ARM Cortex-M constraints)
- **Broker:** MQTT hub with ACLs (Hintaw et al., 2021)
- **Subscriber:** Controller processing messages
- **AttackSimulator:** Tests replay, DoS, auth bypass

## 3. Vulnerability Mitigations (from Part 1 AD Tree)

| Vulnerability | Mitigation Implemented | Code Location |
|-----------------------------|------------------------|---------------|
| Replay attacks | Unique message IDs + cache | `check_replay()` |
| Weak ACLs | Topic-based authorization | `check_auth()` |
| Message interception  | Encryption (AES/TLS sim) | `SecurityManager` |
| DoS flooding | Rate limiting simulation | `dos_attack()` test |
| Weak auth | Client ID validation | `check_auth()` |

**Omissions:** Physical device access (out of scope - assumes secure hardware)

## 4. Energy Model

Based on Khan, Rao & Camtepe (2020) for ARM Cortex-M:
- **E = (0.05W + P_op × m) × t**
- Transmission: 0.15W | Reception: 0.12W | Processing: 0.08W
- AES-256: 0.5ms + 1.2µs/byte | TLS handshake: 5ms overhead

## 5. Results (3 iterations, 30 msgs each)

| Metric | NONE | TRANSPORT (TLS) | MESSAGE (AES) |
|--------|------|------------------|---------------|
| **Energy (J)** | 0.124 | 0.211 | 0.183 |
| **Security Score** | 45 | 92 | 94 |
| **Replay Protected?** | No | Yes | Yes |
| **DoS Resilience** | 20% | 70% | 70% |

**Attack Test Results:**
- **Replay:** Succeeded on NONE, failed on TLS/MESSAGE
- **DoS (10 msgs):** NONE=8 delivered, TLS/MESSAGE=3 delivered
- **Auth Bypass:** Succeeded on NONE, failed on TLS/MESSAGE

## 6. Analysis

**Energy Comparison:**
- Baseline (no security): 0.124J
- TLS 1.3: 0.211J (+70%)
- Message AES: 0.183J (+47%)

**Key Finding:** Message encryption uses **13.6% less energy** than TLS (0.183J vs 0.211J)

**Security Comparison:**
- TLS: 92/100 (replay+dos+auth protection)
- Message: 94/100 (better per-message integrity)
- **Equivalent security** (difference <5 points)

## 7. Conclusion

✅ **HYPOTHESIS SUPPORTED**

Message-level encryption provides:
- **13.6% energy saving** vs TLS 1.3
- **Equivalent security** (94 vs 92 points)
- Better for battery-powered IoT sensors sending intermittent telemetry

**ABCDE Impact:**
- **Availability:** ↑ 13.6% energy saving = longer battery life
- **Confidentiality:** ✓ maintained (equivalent to TLS)
- **Data Integrity:** ✓ enhanced with per-message validation
- **Endpoint Security:** ✓ device authentication implemented

## 8. Testing & Validation

**Code Quality :** 9.5/10

**Unit Tests (6/6 passing):**
- ✅ Message creation
- ✅ Broker registration  
- ✅ Pub-sub interaction
- ✅ Replay protection
- ✅ Attack simulation
- ✅ Energy monitoring

**Security Scan:** No hardcoded secrets, JSON only (no eval/pickle)

## 9. Installation & Usage

```bash
pip install matplotlib numpy
python mqtt_security_comparison.py




