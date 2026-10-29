# Admin Domain — Payment Webhooks & Automation

This document specifies the Deliverable 2 (E2) requirements for receiving asynchronous payment notifications (webhooks) from Stripe Test Mode, verifying their authenticity, preventing duplicates, and providing audit visibility to Treasury staff.

---

## RF-V3a · Receive Payment Webhook (Minimum Level)
- **Milestone:** Deliverable 2 (E2 — Mandatory for E2)
- **Actor:** Treasury / System Integration
- **User Story:** As treasury staff, I want the payment gateway's payment notification to reach Salesforce automatically, so that donations update themselves without manual intervention.

### Acceptance Criteria
- **CA1 (Webhook Ingestion & Status Update):** **Given** a donor paid using a test card in Stripe, **when** the payment gateway sends its payment notification (webhook), **then** the notification reaches Salesforce and the corresponding `Pending` donation transitions to `Paid` (triggering the automated receipt defined in `RF-D5`).

---

## RF-V3b · Authentic & Idempotent Webhooks (Second Level)
- **Milestone:** Deliverable 2 (E2 — Optional / Advanced Level)
- **Actor:** Treasury / System Security
- **User Story:** As treasury staff, I want Salesforce to accept only authentic payment notifications and ensure repeated notifications do not duplicate records or receipts, so that no external actor can mark donations as paid without paying.

### Acceptance Criteria
- **CA1 (Cryptographic Signature Verification):** **Given** an actor other than the payment gateway sends a forged or unsigned notification, **when** it reaches Salesforce, **then** the system rejects it, the donation status remains unchanged, and the rejection is logged and visible to staff (`RF-S4`).
- **CA2 (Idempotency / Deduplication):** **Given** the payment gateway sends the exact same payment notification twice, **when** both arrive at Salesforce, **then** only a single donation is marked `Paid` (campaign total is not double-counted) and only a single email receipt is sent.

---

## RF-S4 · View Automatic Confirmations and Rejected Webhooks
- **Milestone:** Deliverable 2 (E2)
- **Actor:** Staff (Treasury)
- **User Story:** As treasury staff, I want to see which donations were confirmed automatically by the gateway and which incoming webhook notifications were rejected, so that I can trust the financial data without auditing every transaction manually.

### Acceptance Criteria
- **CA1 (Confirmation Source Distinction):** **Given** a donation was confirmed via a payment gateway webhook, **when** staff views the donation in Salesforce, **then** they can clearly distinguish that it was confirmed *automatically* rather than *manually* (`RF-S3`).
- **CA2 (Rejected Webhook Audit Log):** **Given** an incoming webhook notification was rejected (e.g., invalid signature, malformed payload, or unmatched reference), **when** staff reviews the rejected notifications view/log in Salesforce, **then** they can see the timestamp of arrival and the reason for rejection.
