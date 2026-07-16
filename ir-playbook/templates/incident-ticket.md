# Incident Ticket Template

**Ticket ID:** INC-[YYYY]-[####]
**Date/Time Opened:** [YYYY-MM-DD HH:MM UTC]
**Opened By:** [Analyst Name]
**Severity:** [SEV-1 / SEV-2 / SEV-3 / SEV-4]

---

## Alert Details

| Field | Value |
|-------|-------|
| **Alert Source** | [SIEM / EDR / DLP / User Report / HR Notification / Other] |
| **Alert ID/Rule** | [Alert identifier from source system] |
| **Timestamp** | [When the alert fired or activity occurred] |
| **Affected User(s)** | [Username(s)] |
| **Affected System(s)** | [Hostname(s), IP(s)] |
| **Initial Description** | [One paragraph — what happened] |

---

## Triage Rationale

> Document WHY you assigned this severity. Reference contextual modifiers if applicable.

**Severity Assigned:** [SEV-X]
**Rationale:** [Why this severity — what factors did you weigh?]
**Contextual Modifiers Applied:** [Departing employee / Privileged access / Regulated data / Competitor / Pattern — or "None"]

---

## Investigation Timeline

| Timestamp (UTC) | Action | Performed By |
|-----------------|--------|-------------|
| | | |

---

## Evidence Log

| # | Artifact | Source | Collection Time | SHA-256 Hash | Collected By | Authorization |
|---|----------|--------|----------------|-------------|-------------|---------------|
| 1 | | | | | | |

---

## Decision Log

| Timestamp | Decision | Rationale | Decided By |
|-----------|----------|-----------|-----------|
| | | | |

---

## Containment Actions Taken

| Action | Timestamp | Executed By | Authorized By |
|--------|-----------|------------|---------------|
| | | | |

---

## Findings Summary

[To be completed during/after investigation]

---

## Resolution

**Root Cause:** [What caused the incident]
**Resolution Steps:** [What was done to resolve it]
**Date/Time Closed:** [YYYY-MM-DD HH:MM UTC]
**Final Severity:** [May differ from initial if reclassified]
**Closed By:** [Analyst Name]

---

## Post-Incident

- [ ] Lessons learned meeting scheduled (within 5 business days)
- [ ] Playbook updates identified
- [ ] Detection rules reviewed/updated
- [ ] Metrics recorded (MTTD, MTTR, MTTC)
