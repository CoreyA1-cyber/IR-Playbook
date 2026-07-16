# Tabletop Exercise Log — Insider Threat Scenario

**Exercise Date:** July 2026
**Participants:** SOC Analyst (Corey Armstrong), IR Lead (simulated)
**Scenario:** Departing DBA exfiltrating data to direct competitor

---

## Inject 1 — DLP Alert (Detection & Triage)

**Situation:** DLP alert fired on Marcus Webb uploading 185,000 customer PII records to personal Google Drive. Marcus is a departing employee heading to a competitor.

**Decisions Made:**
- Initially classified as SEV-3 / Medium based on playbook's "DLP policy violation" example
- Decided to pull 14 days of activity logs before taking any other action
- Did NOT contact Marcus (correct — avoid tipping off the subject)
- Did NOT disable account (correct — maintain monitoring capability)

**Outcome:** Log review revealed a 12-day systematic exfiltration campaign across multiple platforms (Salesforce, M365, SharePoint, VPN, USB).

**Finding:** Severity classification was too low. The playbook's severity matrix doesn't account for contextual factors (departing employee + competitor + PII). A DLP alert in isolation might be SEV-3, but with insider threat context it should have been SEV-2 from the start.

---

## Inject 2 — Scope Expansion (Analysis)

**Situation:** Activity logs showed 12 days of systematic exfiltration — Salesforce exports escalating in size, confidential emails forwarded to personal Gmail, USB sync, after-hours database access, file sync tool installed.

**Decisions Made:**
- Reclassified to SEV-1 / Critical (confirmed data breach + regulatory exposure)
- Identified email forwards to personal Gmail as highest concern (data irrecoverable, strategic content, zero organizational control over personal accounts)
- Decided to involve both HR and Legal simultaneously

**Outcome:** Correct escalation, correct reasoning. The concern about email forwards showed understanding that server-side data is recoverable but personal accounts are beyond organizational reach.

**Finding:** Detection gap — activity spanned 12 days before the first alert. Earlier detection was possible through UEBA baselines, HR-triggered access reviews on resignation, or Salesforce export monitoring.

---

## Inject 3 — Containment Decision (After-Hours Gap)

**Situation:** 5:30 PM — HR and Legal both listed as "business hours" in the contact roster. Marcus still active. Decision needed on containment.

**Decisions Made:**
- Chose to contain immediately — reduce access and brief HR/Legal tomorrow
- Correctly identified that the playbook provides no guidance for this situation

**Outcome:** Immediate containment was defensible but risked tipping off the subject. The stronger play was partial/silent containment — restrict sensitive data access (Salesforce exports, SharePoint, DLP to block mode) while keeping the account active. This would have stopped the data flow without alerting Marcus, buying time until HR/Legal were available.

**Finding:** Playbook has no after-hours escalation path for HR or Legal. No tiered containment authority matrix defining who can approve what level of containment in the absence of specific stakeholders.

---

## Inject 4 — Evidence & Legal (Preservation Gaps)

**Situation:** Legal questioned whether evidence would hold up. Multiple preservation gaps discovered: no triage documentation, Salesforce logs not exported, no laptop image, no data classification policy, unauthorized email review.

**Decisions Made:**
- Identified forensic laptop image as the most damaging evidence gap
- Chose to wait for Legal authorization before imaging

**Outcome:** The missing data classification policy was actually the most damaging — it undermines the entire legal foundation ("show me where this data was defined as confidential"). The laptop image is important but server-side logs exist independently. Waiting for Legal was proper instinct but wrong timing — Legal was in the room and could authorize immediately.

**Finding:** Playbook needs pre-authorized forensic procedures for SEV-1/2 incidents. Evidence preservation steps should not require per-incident legal authorization for standard actions on corporate-owned assets.

---

## Inject 5 — Escalation Chaos (Communication Failures)

**Situation:** Marcus's manager overheard IT discussing the investigation, directly asked Marcus, who then left the building. CISO asked about breach notification obligations. GDPR clock potentially already ticking.

**Decisions Made:**
- Correctly identified cascading communication failures (OPSEC protocol missing)
- Identified Privacy Officer/DPO as the correct role for breach notification determination (not in playbook or contact roster)
- Chose to contact building security to monitor exits

**Outcome:** Communication failure was the most preventable issue in the entire exercise. The investigation was blown by a hallway conversation. Primary action should have been immediate account lockdown (covert phase was over once Marcus was tipped off) with HR calling him, rather than physical monitoring.

**Finding:** Playbook needs investigation OPSEC protocols (need-to-know lists, code names, explicit instructions to involved teams). Playbook needs Privacy Officer/DPO in the IRT and contact roster. Playbook needs guidance on covert-to-overt investigation transition.

---

## Summary

| Inject | Primary Finding | Gap Type |
|--------|----------------|----------|
| 1 | Severity matrix lacks contextual factors | Process |
| 2 | 12-day detection gap — no HR-triggered access review | Detection |
| 3 | No after-hours escalation for HR/Legal | Availability |
| 3 | No containment authority matrix | Authority |
| 4 | No data classification policy referenced | Organizational |
| 4 | Email review authorization undefined | Legal/Compliance |
| 4 | No pre-authorized forensic procedures | Process |
| 5 | No investigation OPSEC protocol | Communication |
| 5 | Privacy Officer/DPO missing from IRT | Roles |
| 5 | No covert-to-overt transition guidance | Process |
