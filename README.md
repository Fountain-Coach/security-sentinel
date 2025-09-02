# Security Sentinel

## Overview
The **Security Sentinel** is a protective gateway plugin and service designed to safeguard the FountainAI ecosystem against destructive, malicious, or unintended operations. Acting as an intelligent checkpoint, it intercepts potentially risky requests and issues one of three possible outcomes:

- **Allow** – the action is safe to proceed
- **Deny** – the action is rejected as unsafe
- **Escalate** – the action requires manual approval or additional review

This approach ensures that no high-risk instruction reaches core systems unchecked.

---

## How It Works

### 1. Gateway Interception
The `SecuritySentinelGatewayPlugin` integrates into the FountainAI gateway. When a request appears destructive (e.g., deleting data, modifying configurations, or executing suspicious instructions), the plugin routes it to the Sentinel.

### 2. Sentinel Consultation
The request is passed to the `/sentinel/consult` API, which evaluates a natural-language **summary** of the operation along with contextual metadata. The Sentinel then decides whether to allow, deny, or escalate the request.

### 3. LLM-Powered Risk Assessment
Originally built on keyword-based rules, the Sentinel now supports an **LLM-backed analysis engine**. This provides deeper semantic understanding, allowing it to detect subtle threats such as prompt injection, secret exfiltration, or misuse that rule-based filters might miss.

### 4. Failover and Resilience
The Sentinel supports configurable fail modes:
- `fallback` → use rule-based checks if LLM service is unavailable
- `allow` → default to allowing operations if Sentinel is down
- `deny` → default to rejecting operations if Sentinel is down

This ensures the system remains reliable without sacrificing auditability.

### 5. Auditability and Logging
Every decision is logged in a structured, tamper-resistant way:
- `request_id`, `timestamp`, and latency
- Decision outcome and source (LLM or fallback)
- SHA-256 hashes of sensitive inputs, never raw text

This provides a transparent and secure audit trail.

---

## Features
- **Swift-native implementation** for integration with FountainAI’s gateway
- **Extensible design** via the `SecuritySentinelClient` protocol, supporting both LLM and rule-based clients
- **Environment-driven configuration**, including URL, API key, retries, timeouts, and failover mode
- **Circuit breaker and retry logic** for robust error handling
- **Structured logging** for compliance and investigation
- **Comprehensive test suite** (unit + integration) covering success, error, and failover cases

---

## Configuration
Enable and configure the Sentinel through environment variables:

```bash
SEC_SENTINEL_ENABLED=true
SEC_SENTINEL_URL=https://llm-gateway/sentinel/consult
SEC_SENTINEL_API_KEY=changeme
SEC_SENTINEL_TIMEOUT_MS=4000
SEC_SENTINEL_RETRIES=1
SEC_SENTINEL_MODEL=
SEC_SENTINEL_FAIL_MODE=fallback   # fallback | allow | deny
```

---

## Why It Matters
Security Sentinel enforces **defense-in-depth** across the FountainAI platform:
- Protects against **prompt injection**, **supply-chain attacks**, **data corruption**, and **DoS attempts**
- Ensures every risky instruction is subject to **AI-backed security review**
- Provides **transparent, auditable reasoning** for all security decisions
- Balances **safety and reliability** with configurable failover policies

By combining policy enforcement with AI-driven analysis, the Security Sentinel ensures the resilience, safety, and trustworthiness of the FountainAI ecosystem.
