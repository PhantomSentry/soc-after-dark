# 👻 SOC After Dark – Lab 01 Solution
## Investigation Report – The Protected Front Door

---

## Executive Summary

An external attacker successfully exfiltrated customer data by abusing an API endpoint that lacked object-level authorization controls.

Although the Web Application Firewall (WAF) successfully blocked several SQL injection and login abuse attempts, the actual breach occurred through legitimate API requests that were not monitored for behavioral anomalies.

This incident demonstrates a failure in business logic security and detection coverage, not perimeter defense.

---

## Step 1 – Initial WAF Activity

Reviewing `waf.log`, we observe:

- SQL injection attempts from IP `185.22.11.4`
- Rate-limited login attempts
- Multiple blocked signature-based attacks

At this stage, the SOC likely concluded:

> “The WAF is functioning correctly. Threats are being blocked.”

However, blocking injection attempts does not confirm system integrity.

---

## Step 2 – API Activity Review

Inspection of `api.log` reveals:

At `10:02:14`, IP `185.22.11.4` begins sending repeated POST requests to:

    /api/v1/export

Within seconds, the same IP requests:

- UserID=1
- UserID=2
- UserID=3
- UserID=4
- UserID=5

All requests return HTTP 200.

This indicates:

- No authorization enforcement
- No object-level validation
- No session binding

---

## Step 3 – Identifying the Attack Technique

The attacker exploited an Insecure Direct Object Reference (IDOR).

The API trusted the supplied `UserID` parameter without verifying whether the requester was authorized to access that user’s data.

This is not injection.

This is business logic abuse.

---

## Step 4 – Second-Stage Indicator: Data Exfiltration Pattern

Each export request shows large byte responses:

- ~19,000 bytes per request
- Multiple exports within seconds
- Sequential numeric user access

This pattern strongly indicates structured data dumping.

The behavior is more important than the payload.

The spike in cumulative export size is the true indicator of compromise.

---

## Step 5 – Why the WAF Did Not Detect This

The WAF operates on:

- Signature detection
- Keyword inspection
- Injection patterns
- Rate limiting on login endpoints

The attacker:

- Used valid JSON
- Avoided injection patterns
- Targeted an API endpoint not tightly controlled
- Exploited authorization weakness

The WAF worked as configured.

The detection strategy failed.

---

## Root Cause

Primary failure:

- Missing object-level access control (IDOR)

Secondary failure:

- No behavioral monitoring on export endpoints
- No anomaly detection for sequential object access
- Over-reliance on perimeter defenses

---

## Detection Improvements

This attack could have been detected by:

- Monitoring sequential UserID access from a single IP
- Alerting on high-frequency export requests
- Alerting on cumulative outbound data thresholds
- Flagging export behavior from external IPs

Behavior-based detection is essential for API security.

---

## Leadership Takeaway

The SOC assumed:

> “Blocked attacks mean protected system.”

This incident proves:

- WAF ≠ complete protection
- Signature detection ≠ breach prevention
- APIs require explicit authorization validation
- Business logic abuse is invisible to traditional perimeter tools

This was not a failure of the WAF.

It was a failure of detection coverage and application security design.
