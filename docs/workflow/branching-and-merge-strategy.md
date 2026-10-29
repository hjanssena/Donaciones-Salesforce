# Branching & Merge Strategy

We follow a **Feature Branch + Pull Request (Squash and Merge)** workflow to keep `main` stable, auditable, and ready for release at all times.

---

## 1. Core Principles

1. **`main` is Protected:** Direct pushes to `main` are prohibited. All changes must enter `main` through a Pull Request (PR).
2. **Short-Lived Feature Branches:** Every user story, bug fix, or documentation task is developed in its own isolated branch created from the latest `main`.
3. **Mandatory Peer Review:** Every PR requires at least **1 approval** from another team member before merging.
4. **Squash and Merge Only:** PRs are merged into `main` using **Squash and Merge**, producing a single, atomic Conventional Commit on `main` per feature/task.

---

## 2. Branch Naming Convention

Use lowercase kebab-case prefixed by the work category and, when applicable, the requirement ID:

```text
<category>/<rf-id-or-topic>-<short-description>
```

### Examples
- `feat/rf-d1a-campaign-progress`
- `feat/rf-f1-rfc-validation`
- `fix/rf-d3-guest-user-permissions`
- `docs/security-test-plan`
- `chore/husky-commitlint-setup`

---

## 3. Step-by-Step Pull Request Workflow

### Step 1: Start from an Updated `main`
```bash
git checkout main
git pull origin main
git checkout -b feat/rf-s1-campaign-management
```

### Step 2: Develop, Retrieve Metadata & Commit
Make your changes, retrieve any updated Salesforce metadata (`sf project retrieve start`), update [AI/log.md](file:///Users/janssen/Documents/UADY/Semestre7/Salesforce/Proyecto/AI/log.md) (and `docs/bitacora_ia.md` if applicable), and commit following Conventional Commits:
```bash
git add .
git commit -m "feat(rf-s1): add campaign custom fields and validation rule"
git push -u origin feat/rf-s1-campaign-management
```

### Step 3: Open a Pull Request
Open a PR on GitHub targeting `main`. The PR description should include:
- **Associated RF / Backlog ID** (e.g., `RF-S1`).
- **Summary of Changes** (Apex, Flows, LWC, Metadata, or Docs).
- **Verification Evidence** (Unit test results, Code Analyzer pass, or screenshot).

### Step 4: Code Review Checklist
The reviewing teammate must verify:
- [ ] **Zero Secrets (`M3`):** No API keys, tokens, or real PII exist in code, metadata, or screenshots.
- [ ] **Guest User Security (`M2`):** No excessive read/modify permissions granted on `Donation` records.
- [ ] **Salesforce Best Practices (`C6`):** No SOQL/DML inside loops (`S12`), no hard-coded Record IDs (`S14`), and clean error handling (`S11`).
- [ ] **AI Logs Updated:** `AI/log.md` (and `docs/bitacora_ia.md` when applicable) are up to date.

### Step 5: Squash and Merge
- Once approved, merge the PR using GitHub's **"Squash and merge"** button.
- Ensure the final squashed commit title follows **Conventional Commits** (e.g., `feat(rf-s1): implement campaign management and goal validation (#12)`).
- Delete the feature branch after merging.
