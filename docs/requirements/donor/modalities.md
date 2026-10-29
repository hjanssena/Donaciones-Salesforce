# Donor Domain — Donation Modalities

Donors on the public portal can choose from three distinct modalities:
1. **Anonymous Donor (`RF-D2`)** — Contributes without public recognition or tax invoice.
2. **Credited Donor (`RF-C1`)** — Contributes and appears on the campaign's public donor wall.
3. **Invoiced Donor (`RF-F1`)** — Contributes and provides tax details (Name + Mexican RFC) for administrative tracking.

---

## RF-D2 · Donate Anonymously
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Anonymous Donor
- **User Story:** As an anonymous donor, I want my name not to appear in any public place, so that I can support the program without exposing my identity.

### Acceptance Criteria
- **CA1 (No Public Exposure):** **Given** I marked my donation as anonymous, **when** I inspect the public site (campaign cards, donor recognition lists, and the thank-you page), **then** my name does not appear anywhere.
- **CA2 (Anonymous Receipt):** **Given** my anonymous donation is marked as `Paid`, **when** I receive my email receipt, **then** the receipt does not display my name.
- **CA3 (Mutual Exclusivity with Invoice):** **Given** I marked my donation as anonymous, **when** I attempt to request an invoice, **then** I am prevented from doing so (the anonymous modality cannot request an invoice).
- **CA4 (Internal Staff Visibility):** **Given** an anonymous donation exists, **when** staff queries donations in Salesforce, **then** it is clearly identified as `Anonymous`.

---

## RF-C1 · Appear as a Credited Donor
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Credited Donor
- **User Story:** As a credited donor, I want my name to appear as a supporter of the campaign, so that my contribution is publicly recognized.

### Acceptance Criteria
- **CA1 (Public Recognition List for Paid Donations):** **Given** I requested credit and my donation is in `Paid` status, **when** any visitor opens that campaign on the public site, **then** they see my name and donation amount in the campaign's donor recognition list. The recognition list is per-campaign and only includes `Paid` donations (`Pending` donations are excluded).
- **CA2 (Strict PII Minimization):** **Given** I requested credit, **when** I inspect the public donor recognition list, **then** *only* my name and donation amount are displayed; my email address and any other personal data are never shown.

---

## RF-F1 · Capture Tax Data for Invoice (RFC)
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Invoiced Donor
- **User Story:** As a donor requesting an invoice, I want to enter my legal name and RFC, so that the program has my tax information to process my invoice request.

### Acceptance Criteria
- **CA1 (Mandatory Name & RFC):** **Given** I chose to request an invoice, **when** I attempt to submit the donation form without a name or without an RFC, **then** the submission is blocked and the form clearly indicates which required field is missing.
- **CA2 (SAT RFC Pattern Validation):** **Given** I entered an RFC, **when** I submit the form, **then** the system validates it against the official SAT structural pattern (initial letters, 6-digit date, and 3-character homoclave: 12 characters for legal entities / *persona moral*, 13 characters for individuals / *persona física*) and rejects any input that does not match. The system does *not* query the live SAT external service.
- **CA3 (No Real CFDI Generation):** **Given** the invoiced donor completed their donation, **when** the system is audited, **then** no real fiscal receipt (CFDI) is emitted.

> [!NOTE]
> **Cross-Reference:** See [admin/treasury-donations.md](file:///Users/janssen/Documents/UADY/Semestre7/Salesforce/Proyecto/docs/requirements/admin/treasury-donations.md) (`RF-F2`) for how staff views and filters invoice requests in the backoffice.
