# API Security Risk Analysis & Assessment

**Internship Track:** Cyber Security  
**Task Code:** FUTURE_CS_03  
**Auditor:** G. Jagadeeshwar Reddy  
**Contact:** +91 6304214258 | jagadeeshguduru006@gmail.com  
**Assessment Date:** November 2026  

---

## 1. Executive Summary
This repository documents a comprehensive, read-only API Security Risk Analysis conducted under the Future Interns Cyber Security Internship. Aligned with the **OWASP API Security Top 10**, this engagement evaluates public REST endpoints (`https://dummyjson.com` and `https://jsonplaceholder.typicode.com`) across five core security dimensions: unauthenticated data exposure, broken object-level authorization (BOLA/IDOR), JWT authentication handling, rate-limiting and CORS headers, and input validation.

---

## 2. Repository Architecture

    FUTURE_CS_03/
    ├── Evidence/
    │   ├── 1_excessive_data_exposure.png          # Unauthenticated PII & credential exposure on /users/1
    │   ├── 2_jwt_auth_login.png                   # JWT accessToken & refreshToken issuance on /auth/login
    │   ├── 3_invalid_token_401.png                # 401 Unauthorized enforcement on protected /auth/me
    │   ├── 4_headers_rate_limit.png               # Permissive rate limit & CORS headers on /users
    │   └── 5_input_validation.png                 # Malformed ID error reflection on /users/invalid_id_test
    ├── Report/
    │   └── API_Security_Risk_Analysis_Report.pdf  # Final consulting-style security report & guide
    └── README.md                                  # Project overview, OWASP findings, and remediation guide

---

## 3. Assessment Methodology & Tools Utilized

1. **Endpoint Inspection & HTTP Traffic Analysis:**
   - **Chrome DevTools (Network & Console) / Postman:** Executed controlled, non-destructive `GET` and `POST` requests to inspect JSON response payloads, HTTP status codes, authentication headers, and CORS policies.
2. **OWASP API Security Top 10 Mapping:**
   - Evaluated endpoints against **API1:2023** (Broken Object Level Authorization), **API2:2023** (Broken Authentication), **API3:2023** (Broken Object Property Level Authorization), **API4:2023** (Unrestricted Resource Consumption), and **API8:2023** (Security Misconfiguration).

---

## 4. API Security Findings Summary

| ID | Finding & OWASP Category | Target Endpoint | Severity | Key Technical & Business Impact |
|---|---|---|---|---|
| **01** | **Unauthenticated Excessive Data Exposure & BOLA/IDOR** (`API1` / `API3`) | `GET https://dummyjson.com/users/1` | **High** | Returns plaintext `password`, `ssn`, `ein`, `bank.cardNumber`, `iban`, `crypto.wallet`, and `ip` without authentication via predictable sequential IDs. |
| **02** | **JWT Bearer Token Issuance & Client Storage Exposure** (`API2`) | `POST https://dummyjson.com/auth/login` | **Medium** | Returns long-lived `accessToken` and `refreshToken` directly in JSON response bodies; vulnerable to XSS theft if stored in browser `localStorage`. |
| **03** | **Protected Endpoint Access Control Verification** (`API2`) | `GET https://dummyjson.com/auth/me` | **Informational** | Properly rejects unauthenticated or invalid Bearer requests with `401 Unauthorized` (`"Access Token is required"`). |
| **04** | **Excessive Rate Limit Quota, Credentialed CORS & Banner Leak** (`API4` / `API8`) | `GET https://jsonplaceholder.typicode.com/users` | **Medium** | Exposes `X-RateLimit-Limit: 3600000`, `Access-Control-Allow-Credentials: true`, and `X-Powered-By: Express`, enabling high-speed automated scraping and stack fingerprinting. |
| **05** | **Input Validation & Unsanitized Error Reflection** (`API8`) | `GET https://dummyjson.com/users/invalid_id_test` | **Low** | Blocks non-integer input with `400 Bad Request` without stack traces, but reflects raw user input (`'invalid_id_test'`) inside the error message. |

---

## 5. Business Risk Explanation & Developer Best Practices

### Why API Security Matters to the Business
* **Data Breaches & Regulatory Fines:** Unauthenticated endpoints returning excessive user fields expose customer PII and financial data, leading to GDPR/DPDP compliance penalties and reputational damage.
* **Account Takeover:** Weak token storage or missing object-level authorization allows attackers to enumerate user IDs and hijack customer accounts.
* **Service Degradation & Scraping:** Missing rate limits allow automated bots to harvest proprietary catalog data or exhaust backend infrastructure.

### API Security Do's and Don'ts

| DO (Secure API Design) | DON'T (High-Risk Anti-Patterns) |
|---|---|
| Enforce authentication and object-level ownership checks on every private endpoint. | Never return raw database objects containing passwords, SSNs, or internal flags. |
| Issue short-lived JWTs stored in `HttpOnly`, `Secure`, `SameSite=Strict` cookies. | Never store sensitive Bearer tokens in `localStorage` where XSS scripts can read them. |
| Apply strict per-IP and per-token rate limiting (e.g., 60–100 requests/minute). | Never combine `Access-Control-Allow-Credentials: true` with wildcard or untrusted origins. |
| Validate input types strictly and return static, generic JSON error messages. | Never reflect unsanitized user input or expose `X-Powered-By` server headers. |

---

## 6. Final Deliverable
The complete, formatted security assessment report and remediation guide is available at:  
👉 **`Report/API_Security_Risk_Analysis_Report.pdf`**