# SOC After Dark Labs
# 👻 SOC After Dark – Lab 01  
## The Protected Front Door

**Category:** Web / API Abuse / Detection Failure  

---

## 🧠 Scenario

The AI Alchemist’s public web application is protected by a Web Application Firewall (WAF).

SOC dashboard shows:

- SQL injection attempts blocked
- Login rate limiting active
- No critical alerts
- System status: 🟢 Healthy

One week later, customer records appear for sale online.

You have been provided partial logs from:

- logs/waf.log
- logs/api.log

Investigate what happened.

---

## 🎯 Objectives

1. What attack technique was successfully used?
2. Which IP address is malicious?
3. What vulnerability class was exploited?
4. Why did the WAF fail to stop the breach?
5. What is the second-stage indicator of compromise?
6. What detection logic would have caught the second stage?

---

## 🚩 Flag Format

FLAG{your_answer_here}

Example:
FLAG{idor_vulnerability}

---

## 🕵️ Hint

The breach did not rely on SQL injection.

Look beyond the login page.
