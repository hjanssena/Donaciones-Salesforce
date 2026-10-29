# Admin Domain — Campaign Management

This document covers the internal requirements for program coordination staff to configure and maintain fundraising campaigns in Salesforce.

---

## RF-S1 · Manage Campaigns
- **Milestone:** Deliverable 1 (E1)
- **Actor:** Staff (Program Coordination)
- **User Story:** As program coordination staff, I want to create and manage campaigns with their respective financial goals, so that I can organize the fundraising efforts for Mexico Dreamin.

### Acceptance Criteria
- **CA1 (Campaign Creation & Publication):** **Given** I am an authenticated staff member in Salesforce, **when** I create an active campaign with a name and target goal amount, **then** the campaign appears on the public donation portal.
- **CA2 (Goal Updates Reflected Publicly):** **Given** an active campaign exists, **when** a staff member updates its target goal amount in Salesforce, **then** the public site reflects the updated goal amount upon reload.
- **CA3 (Negative Case — Invalid Goal Amount):** **Given** I am creating or editing a campaign, **when** I attempt to set a target goal less than or equal to zero, **then** the system prevents saving the record and displays a validation error.
