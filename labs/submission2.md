# Lab 2 — Threat Modeling with Threagile

## Task 1 — Threagile Baseline Model (6 pts)

### Top 5 Risks

| Rank | Risk | Severity | Category | Asset | Likelihood | Impact | Score |
|---:|---|---|---|---|---|---|---:|
| 1 | Unencrypted Communication — Direct to App (no proxy) | Elevated | unencrypted-communication | User Browser → Juice Shop | Likely | High | **433** |
| 2 | Cross-Site Scripting (XSS) | Elevated | cross-site-scripting | Juice Shop Application | Likely | Medium | **432** |
| 3 | Missing Authentication — To App | Elevated | missing-authentication | Reverse Proxy → Juice Shop | Likely | Medium | **432** |
| 4 | Unencrypted Communication — To App | Elevated | unencrypted-communication | Reverse Proxy → Juice Shop | Likely | Medium | **432** |
| 5 | Cross-Site Request Forgery (CSRF) | Medium | cross-site-request-forgery | Juice Shop Application | Very-likely | Low | **241** |

### Analysis of Critical Security Concerns

**1. Unencrypted Communication (433)**: Direct user-to-app traffic is completely exposed. This is the highest risk, allowing attackers to steal credentials and data in transit with minimal effort.

**2. Missing Authentication (432)**: The reverse proxy allows direct access to the app without login. This is a critical perimeter failure, granting unauthorized users full access to internal functions.

**3.XSS (432)**: Successful injection allows attackers to execute malicious scripts in users' browsers, leading to session hijacking and data theft.

**4.Unencrypted Communication (432)**: Even internal traffic between the proxy and the app is unencrypted, exposing data to anyone with access to the internal network.

**5.CSRF (241)**: Although the score is lower, the "Very Likely" likelihood means state-changing requests can be forged easily if a user is authenticated, leading to unintended actions.



### Diagrams

The generated diagrams are located in `labs/lab2/baseline/`:

- **Data-Flow Diagram** (`data-flow-diagram.png`): Shows User Browser, Reverse Proxy, Juice Shop Application, Persistent Storage, and Webhook Endpoint with communication links between them.
- **Data-Asset Diagram** (`data-asset-diagram.png`): Maps data assets (Customer Orders, Credentials, Tokens & Sessions) to their processing and storage locations.

---

## Task 2 — HTTPS Variant & Risk Comparison (4 pts)

### Risk Category Delta Table

| Category | Baseline | Secure | Δ |
|---|---:|---:|---:|
| container-baseimage-backdooring | 1 | 1 | 0 |
| cross-site-request-forgery | 2 | 2 | 0 |
| cross-site-scripting | 1 | 1 | 0 |
| missing-authentication | 1 | 1 | 0 |
| missing-authentication-second-factor | 2 | 2 | 0 |
| missing-build-infrastructure | 1 | 1 | 0 |
| missing-hardening | 2 | 2 | 0 |
| missing-identity-store | 1 | 1 | 0 |
| missing-vault | 1 | 1 | 0 |
| missing-waf | 1 | 1 | 0 |
| server-side-request-forgery | 2 | 2 | 0 |
| **unencrypted-asset** | **2** | **1** | **-1** |
| **unencrypted-communication** | **2** | **0** | **-2** |
| unnecessary-data-transfer | 2 | 2 | 0 |
| unnecessary-technical-asset | 2 | 2 | 0 |

**Total risk count: 23 → 20 (Δ = -3)**

### Delta Run Explanation

#### Changes Made

1. **HTTPS on communication links**: Both the direct browser-to-app link and the reverse-proxy-to-app link were switched from `http` to `https`.
2. **Transparent encryption on Persistent Storage**: The database/file store encryption was changed from `none` to `transparent`.

#### Observed Results

- **Unencrypted Communication** risks were fully resolved: both direct and proxy connections now use HTTPS, reducing the category count from 2 → 0.
- **Unencrypted Asset** risk was partially mitigated: enabling transparent encryption reduced the category count from 2 → 1.
- All other risk categories remained unchanged.

#### Why These Changes Reduced Risks

- **HTTPS** encrypts data in transit, preventing eavesdropping, session hijacking, and man-in-the-middle attacks on both user→app and proxy→app links.
- **Transparent encryption** protects data at rest in persistent storage, rendering it unreadable if the underlying storage is compromised.

### Diagram Comparison

| Aspect | Baseline | Secure |
|---|---|---|
| Data-flow diagram | `labs/lab2/baseline/data-flow-diagram.png` | `labs/lab2/secure/data-flow-diagram.png` |
| Data-asset diagram | `labs/lab2/baseline/data-asset-diagram.png` | `labs/lab2/secure/data-asset-diagram.png` |

Key visual differences in the secure variant diagrams:
- Communication links between User Browser → Juice Shop and Reverse Proxy → Juice Shop now show as encrypted (HTTPS) connections
- Persistent Storage is marked with encryption enabled
- The overall risk coloring/indicators on affected assets reflect the reduced risk posture