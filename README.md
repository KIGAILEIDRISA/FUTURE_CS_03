# FUTURE_CS_03
#  API Security Risk Analysis

## Read-Only Assessment of Public Demonstration APIs

![Security Assessment](https://img.shields.io/badge/Assessment-API%20Security-blue)
![OWASP](https://img.shields.io/badge/OWASP-API%20Security%20Top%2010%202023-red)
![Assessment Type](https://img.shields.io/badge/Testing-Read--Only%20%7C%20Non--Intrusive-green)
![Status](https://img.shields.io/badge/Status-Completed-success)

---

## Overview

This repository contains an **API Security Risk Analysis** performed against two publicly available demonstration APIs:

* **JSONPlaceholder** — `jsonplaceholder.typicode.com`
* **ReqRes** — `reqres.in`

The assessment was conducted from a defender's perspective and focused on identifying security weaknesses, understanding their potential business impact, and recommending practical remediation.

The assessment followed the **OWASP API Security Top 10 (2023)** as the primary reference framework.

No production systems were targeted, and no exploitation was performed.

---

## Assessment Information

| Field               | Details                                             |
| ------------------- | --------------------------------------------------- |
| **Prepared by**     | Idrisa Haruni Kigaile                               |
| **Report**          | API Security Risk Analysis Report                   |
| **Assessment Type** | Read-only, non-intrusive API security risk analysis |
| **Targets**         | `jsonplaceholder.typicode.com`, `reqres.in`         |
| **Framework**       | OWASP API Security Top 10 (2023)                    |
| **Assessment Date** | 13 August 2026                                      |

The assessment was limited to publicly available APIs explicitly published for testing and learning.

---

# Objectives

The assessment objectives were to:

* Analyse publicly available demonstration APIs from a defender's perspective.
* Assess authentication and authorization controls.
* Identify potential data exposure.
* Review rate limiting and resource consumption controls.
* Examine API input handling and validation.
* Classify security risks by severity.
* Translate technical weaknesses into business impact.
* Recommend practical and implementable remediation.

---

# Scope

## Targets

| Target              | Purpose                                                 | Testing Performed                                                                |
| ------------------- | ------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **JSONPlaceholder** | Free fake REST API for prototyping and teaching         | GET requests, documented sandbox POST/DELETE, headers, CORS and error inspection |
| **ReqRes**          | Hosted API testing platform with API-key authentication | Authentication behaviour, error handling and response-header review              |

Both APIs are publicly provided for testing and learning.

---

##  Permitted Activities

The assessment included:

* Read-only GET requests.
* Documentation review.
* Inspection of response headers.
* Inspection of status codes.
* Inspection of tokens and response bodies.
* Safe POST/DELETE requests against JSONPlaceholder's documented simulated sandbox.
* A short, bounded 25-request sequence to observe throttling behaviour.

## Explicitly Excluded

The following activities were not performed:

* Exploitation.
* Authentication bypass.
* Privilege escalation.
* Flooding.
* Stress testing.
* Denial-of-service testing.
* Testing private, third-party or production APIs.
* Automated vulnerability scanning.
* Brute-force activity.

The purpose was to identify and communicate risk rather than prove exploitability through intrusive testing.

---

#  Tools Used

| Tool                                 | Purpose                                                                                                |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| **Postman / Insomnia**               | Build and organise request collections, inspect headers, bodies and status codes, and capture evidence |
| **Browser DevTools**                 | Observe requests, CORS preflight behaviour and response headers                                        |
| **curl**                             | Reproducible command-line request capture and evidence collection                                      |
| **OWASP API Security Top 10 (2023)** | Security-risk classification framework                                                                 |
| **API Security Checklist**           | Cross-check practical API security controls                                                            |
| **Microsoft Word**                   | Report production                                                                                      |

---

# Methodology

The assessment followed a structured security-review process:

1. **Target Selection**
   Select demonstration APIs explicitly published for testing.

2. **Documentation Review**
   Review available resources, HTTP methods, authentication mechanisms and documented limitations.

3. **Baseline Requests**
   Send unauthenticated GET requests and record status codes, headers and response bodies.

4. **Authentication Testing**
   Compare responses with no credentials, malformed bearer tokens and invalid API keys.

5. **Authorization Testing**
   Change resource identifiers to determine whether object ownership checks are applied.

6. **Data Exposure Review**
   Review response fields for unnecessary personal or sensitive information.

7. **Rate-Limit Observation**
   Send a short bounded request sequence and inspect rate-limit headers and status codes.

8. **Input Handling**
   Test type mismatches, undeclared fields and specially shaped data against a sandbox write endpoint.

9. **Configuration Review**
   Review CORS, security headers, caching directives and error responses.

10. **Risk Rating**
    Evaluate likelihood and impact and translate findings into business consequences and remediation.

---

# Findings Summary

The assessment identified **nine observations**:

| ID         | Finding                                                         | OWASP API Security Top 10                            | Severity             | Priority |
| ---------- | --------------------------------------------------------------- | ---------------------------------------------------- | -------------------- | -------- |
| **API-01** | Unauthenticated access to all data endpoints                    | API2:2023 Broken Authentication                      | 🔴 **High**          | P1       |
| **API-02** | Excessive exposure of personal information                      | API3:2023 Broken Object Property Level Authorization | 🔴 **High**          | P1       |
| **API-03** | No object-level authorization on record identifiers             | API1:2023 BOLA                                       | 🔴 **High**          | P1       |
| **API-04** | No effective rate limiting on anonymous traffic                 | API4:2023 Unrestricted Resource Consumption          | 🟠 **Medium**        | P2       |
| **API-05** | Input accepted without validation / undeclared fields persisted | API6:2023 / API3:2023                                | 🟠 **Medium**        | P2       |
| **API-06** | Permissive CORS with credentials                                | API8:2023 Security Misconfiguration                  | 🟠 **Medium**        | P2       |
| **API-07** | Missing HTTP security and transport hardening headers           | API8:2023 Security Misconfiguration                  | 🟡 **Low**           | P3       |
| **API-08** | State-changing methods reachable without authentication         | API5:2023 Broken Function Level Authorization        | 🟠 **Medium**        | P2       |
| **API-09** | Enforced API key and clean error handling                       | Positive observation                                 | 🔵 **Informational** | —        |

---

#  Risk Distribution

| Severity             | Count | Findings                       |
| -------------------- | ----: | ------------------------------ |
| 🔴 **High**          |     3 | API-01, API-02, API-03         |
| 🟠 **Medium**        |     4 | API-04, API-05, API-06, API-08 |
| 🟡 **Low**           |     1 | API-07                         |
| 🔵 **Informational** |     1 | API-09                         |

**Total observations: 9**

---

#  Key Findings

## 🔴 API-01 — Unauthenticated Access to All Data Endpoints

**Severity:** High
**OWASP:** API2:2023 Broken Authentication

The JSONPlaceholder data endpoints returned successful responses without an Authorization header, API key or session cookie. A deliberately invalid bearer token also produced a successful response.

### Business Impact

In a production SaaS environment, this pattern could allow anyone who knows the endpoint to access the dataset without an identity, audit trail or effective access control.

### Recommended Remediation

* Require authentication on every non-public endpoint.
* Validate token signature, issuer, audience and expiration.
* Reject malformed or invalid credentials with HTTP `401`.
* Fail closed when credentials are missing or invalid.
* Log authentication decisions for monitoring and incident response.

---

## 🔴 API-02 — Excessive Exposure of Personal Information

**Severity:** High
**OWASP:** API3:2023 Broken Object Property Level Authorization

A single anonymous request returned multiple personal attributes, including:

* Name
* Username
* Email address
* Street address
* Geographic coordinates
* Telephone number
* Website
* Employer

The `/users` endpoint also returned the complete directory without field filtering or a pagination requirement.

### Business Impact

In a production environment, this type of response could provide attackers with a highly useful dataset for phishing, social engineering and other abuse.

### Recommended Remediation

* Return only the fields required by the consuming application.
* Use explicit response schemas/DTOs.
* Restrict sensitive fields using roles or scopes.
* Mask sensitive information where appropriate.
* Enforce server-side pagination and maximum page sizes.

---

## 🔴 API-03 — Broken Object Level Authorization (BOLA)

**Severity:** High
**OWASP:** API1:2023 Broken Object Level Authorization

Changing sequential record identifiers allowed different records to be retrieved without an ownership check.

For example:

```text
/users/1
/users/2
```

The identifier alone was sufficient to retrieve another record.

### Business Impact

In a live SaaS platform, this could become a cross-tenant data exposure where one customer accesses another customer's records.

### Recommended Remediation

* Verify that the authenticated user is authorised to access the requested object.
* Centralise ownership checks in middleware or a policy layer.
* Use UUIDs as additional defence against simple enumeration.
* Add automated cross-tenant authorization tests.

---

## 🟠 API-04 — No Effective Rate Limiting

**Severity:** Medium
**OWASP:** API4:2023 Unrestricted Resource Consumption

A bounded sequence of **25 rapid requests** returned HTTP `200` without a `429` response or throttling delay.

### Business Impact

Without effective identity-bound rate limits, attackers could potentially:

* Enumerate records.
* Abuse authentication endpoints.
* Increase infrastructure consumption.
* Generate unexpected cloud or API costs.

### Recommended Remediation

* Apply limits per authenticated client and IP.
* Use stricter limits for authentication and search endpoints.
* Return HTTP `429` with `Retry-After`.
* Implement quotas and downstream cost controls.
* Monitor abnormal request patterns.

> Testing was deliberately limited to 25 sequential requests. No flooding or denial-of-service testing was performed.

---

## 🟠 API-05 — Insufficient Input Validation / Mass Assignment

**Severity:** Medium
**OWASP:** API6:2023 / API3:2023

The sandbox accepted incorrect data types and undeclared fields, including fields such as:

```json
{
  "title": 123456,
  "userId": "not-a-number",
  "isAdmin": true,
  "role": "superuser"
}
```

The fields were accepted and echoed in the response.

### Business Impact

In a production application, unrestricted fields such as `isAdmin` or `role` could create a privilege-escalation path if they were bound directly to application models.

### Recommended Remediation

* Validate every request body against a strict schema.
* Reject unknown properties.
* Use explicit allow-lists.
* Enforce types, lengths and formats.
* Use parameterised database queries.
* Encode output appropriately.

---

## 🟠 API-06 — Permissive CORS Configuration

**Severity:** Medium
**OWASP:** API8:2023 Security Misconfiguration

The CORS preflight response reflected an attacker-supplied origin while allowing credentials.

Observed behaviour included:

```http
access-control-allow-origin: https://evil.example
access-control-allow-credentials: true
```

### Business Impact

In an affected production application, a malicious website could potentially read authenticated API responses through a customer's browser.

### Recommended Remediation

* Use a static allow-list of trusted origins.
* Do not reflect arbitrary origins.
* Avoid combining reflected/wildcard origins with credentials.
* Restrict allowed HTTP methods and headers.
* Add CORS regression tests.

---

## 🟡 API-07 — Missing Security and Transport Headers

**Severity:** Low
**OWASP:** API8:2023 Security Misconfiguration

The assessment observed missing security headers, including:

```http
Strict-Transport-Security
Content-Security-Policy
X-Frame-Options
Referrer-Policy
```

The response also disclosed the technology stack through:

```http
X-Powered-By: Express
```

Personal-data responses were also observed with permissive caching.

By comparison, ReqRes returned HSTS, `X-Frame-Options: DENY` and `Cache-Control: no-store`.

### Recommended Remediation

* Enable HSTS.
* Use `Cache-Control: no-store` for personal/authenticated data.
* Add appropriate security headers.
* Remove unnecessary technology/version disclosure.

---

## 🟠 API-08 — State-Changing Methods Without Authentication

**Severity:** Medium
**OWASP:** API5:2023 Broken Function Level Authorization

The sandbox allowed anonymous:

```text
POST /posts
DELETE /posts/1
```

The provider documents these operations as simulated and non-persistent.

### Business Impact

If the same authorization model existed in production, anonymous users could potentially create or delete records.

### Recommended Remediation

* Deny functions by default.
* Explicitly assign methods to authorised roles.
* Separate read and write permissions.
* Require additional confirmation for destructive operations.
* Log and monitor write/delete operations.

---

# 🔵 API-09 — Positive Security Control Observed

**Severity:** Informational

ReqRes provided a useful security counter-example.

It:

* Enforced an API key.
* Returned HTTP `401` when authentication was missing or invalid.
* Used structured JSON errors.
* Avoided exposing stack traces or internal paths.

The report identifies this as the desired target state for API authentication and error handling.

---

#  Remediation Roadmap

## Phase 1 — Immediate

**Timeframe:** 0–7 days

Priorities:

* Enforce authentication by default.
* Validate and reject malformed tokens.
* Implement object-level authorization.
* Block anonymous write/delete operations.

**Findings addressed:** API-01, API-03, API-08

---

## Phase 2 — Short Term

**Timeframe:** 2–4 weeks

Priorities:

* Introduce endpoint-specific response schemas.
* Remove unnecessary personal information.
* Enforce pagination.
* Implement strict request-body validation.
* Reject unknown fields.
* Replace CORS origin reflection with a static allow-list.

**Findings addressed:** API-02, API-05, API-06

---

## Phase 3 — Medium Term

**Timeframe:** 1–3 months

Priorities:

* Implement identity-bound rate limits.
* Return `429` and `Retry-After`.
* Establish a complete security-header baseline.
* Use `no-store` for personal data.
* Remove technology/version banners.
* Add cross-tenant authorization tests.
* Add CORS regression tests.
* Centralise authentication and write-operation logging.

**Findings addressed:** API-04, API-07

---

#  API Security Control Checklist

* [ ] **Authentication** — Every non-public endpoint requires validated credentials.
* [ ] **Authorization** — Every object access verifies ownership or entitlement.
* [ ] **Data Minimisation** — Responses expose only required fields.
* [ ] **Pagination** — Collection endpoints enforce pagination and maximum page sizes.
* [ ] **Input Validation** — Strict schemas, type enforcement and unknown-field rejection.
* [ ] **Rate Limiting** — Identity-bound limits, quotas and cost controls.
* [ ] **CORS** — Static allow-list of trusted origins.
* [ ] **Security Headers** — HSTS and appropriate browser security headers.
* [ ] **Caching** — Personal/authenticated data uses appropriate cache controls.
* [ ] **Information Disclosure** — Remove unnecessary technology/version banners.
* [ ] **Monitoring** — Authentication and write operations are logged and monitored.

---

# 📁Repository Structure

```text
.
├── README.md
│
├── report/
│   ├── API_Security_Risk_Analysis_Report.pdf
│   └── API_Security_Risk_Analysis_Report.docx
│
├── screenshots/
│   ├── postman/
│   └── devtools/
│
└── evidence/
    ├── curl/
    └── postman_collection/
```

The original report specifies a repository structure containing the report, screenshots, raw evidence, Postman collection and README.

---

# 📚 References

* **OWASP API Security Top 10 (2023)**
* **API Security Checklist**
* **Public APIs Collection**
* **JSONPlaceholder Documentation**
* **ReqRes API Documentation**

These are the references listed in the original report.

---

#  Ethics & Responsible Testing

All testing was performed against public demonstration APIs explicitly published for testing and learning.

The assessment was:

* Read-only wherever possible.
* Non-intrusive.
* Limited to documented sandbox operations.
* Performed without authentication bypass attempts.
* Performed without exploitation.
* Performed without load or denial-of-service testing.
* Performed without accessing private or production systems.

No real customer data was placed at risk.

> **Security testing should only be performed against systems you own or systems for which you have explicit permission to test.**

---

# Conclusion

The assessment demonstrates two different API security approaches.

**JSONPlaceholder**, intentionally designed as a demonstration API, exposes several weaknesses that would be serious concerns in a commercial SaaS environment, including missing authentication, lack of object-level authorization, excessive data exposure, weak input validation, permissive CORS and missing security hardening.

**ReqRes** provides a useful positive example by enforcing API-key authentication, returning structured `401` errors, and implementing security controls such as HSTS, frame protection and `no-store` caching.

The central lesson from the assessment is that **authentication and authorization should be enforced by the API platform by default rather than being optional controls implemented independently by individual handlers**.

---

##  Author

**Idrisa Haruni Kigaile**

**Cybersecurity Task 3 · 2026**

> API Security Risk Analysis — Read-only assessment of public demonstration APIs

