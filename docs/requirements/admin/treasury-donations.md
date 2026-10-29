# Admin Domain — Treasury & Donation Management

This document covers the internal Salesforce console requirements for Treasury and Staff to oversee donations, filter invoice requests, and manually confirm payments.

---

## RF-S2 · View All Donations
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Staff (Treasury)
- **User Story:** As treasury staff, I want to view all donations along with their key information, so that I can track and audit fundraising activity.

### Acceptance Criteria
- **CA1 (Comprehensive Donation Overview):** **Given** donations exist across all three modalities (`Anonymous`, `Credited`, `Invoiced`) and in various statuses (`Pending`, `Paid`), **when** staff queries or views the donations list in Salesforce, **then** they can see the status, amount, modality, payment reference, and associated campaign for each record.

---

## RF-F2 · Flag and Filter Invoice Requests
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Staff (Treasury / Administration)
- **User Story:** As staff, I want donations that request an invoice to be clearly flagged, so that I can identify and process them.

### Acceptance Criteria
- **CA1 (Invoice Flag & Tax Details):** **Given** a donation with an invoice request exists, **when** staff inspects the donation in Salesforce, **then** they see it flagged as "Requests Invoice" along with the donor's captured legal name and RFC.
- **CA2 (Dedicated Filtering):** **Given** donations of multiple modalities exist in Salesforce, **when** staff filters or searches for donations requesting an invoice, **then** they can isolate and view only those records.

---

## RF-S3 · Confirm Payment Manually
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Staff (Treasury)
- **User Story:** As treasury staff, I want to manually confirm the payment of a pending donation, so that the donor receives their receipt and the campaign progress updates even if automatic confirmation is not yet implemented or fails.

### Acceptance Criteria
- **CA1 (Status Transition to Paid):** **Given** a donation is in `Pending` status, **when** staff manually confirms its payment in Salesforce, **then** the donation status transitions to `Paid` (and its amount is added to the campaign's public progress per `RF-D1a`).
- **CA2 (Receipt Trigger on Manual Confirmation):** **Given** staff manually confirmed the payment, **when** the donor checks the email address they provided, **then** they receive their donation receipt (respecting anonymity rules in `RF-D2` if applicable).
