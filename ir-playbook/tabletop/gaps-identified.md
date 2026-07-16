# Gaps Identified — Tabletop Exercise Review

**Exercise:** Insider Threat / Data Exfiltration Scenario
**Playbook Version Tested:** v1.0
**Total Gaps Found:** 10

---

## Gap 1 — Severity Matrix Lacks Contextual Decision Framework

**Where it broke:** Inject 1 — initial triage
**Problem:** The severity matrix lists example incident types (e.g., "DLP policy violation" → SEV-3) but doesn't instruct analysts to weight contextual factors. A DLP alert on a random employee is different from a DLP alert on a departing employee heading to a competitor with PII.
**Fix:** Add a contextual modifier table — factors that escalate severity regardless of the alert type (departing employee, privileged access, regulated data, competitor involvement, pattern of behavior).

## Gap 2 — SEV-1 Criteria Ambiguity

**Where it broke:** Inject 2 — severity reclassification
**Problem:** SEV-1 definition uses "or" between criteria (active breach OR business halted OR regulatory exposure) but doesn't rank them. Analysts hesitated on whether confirmed PII exfiltration without business disruption qualifies.
**Fix:** Clarify that ANY single SEV-1 criterion is sufficient. Add: "If in doubt, classify UP — downgrading is easier than explaining under-response."

## Gap 3 — No After-Hours Escalation Path for HR/Legal

**Where it broke:** Inject 3 — 5:30 PM containment decision
**Problem:** HR Director and General Counsel listed as "business hours" availability. No backup contacts, no after-hours phone numbers, no guidance on what to do when they're unreachable during an active SEV-1.
**Fix:** Add after-hours contact methods for all critical roles. Define an escalation chain: if Role A is unreachable within 30 minutes, escalate to Role B. Add emergency cell phone numbers to the contact roster.

## Gap 4 — No Containment Authority Matrix

**Where it broke:** Inject 3 — who can authorize containment?
**Problem:** The playbook lists containment actions but doesn't define WHO can authorize each type, especially when HR/Legal are unavailable. The IR Lead didn't know if they had authority to restrict Marcus's access without HR sign-off.
**Fix:** Add a containment authority matrix: action → required approval → after-hours alternative. Example: "Restrict data access (silent) → IR Lead can authorize. Disable account (overt) → requires HR coordination or CISO emergency approval."

## Gap 5 — No Data Classification Policy Referenced

**Where it broke:** Inject 4 — Legal questions evidence strength
**Problem:** The playbook references "sensitive data" and "confidential" but the organization has no formal data classification policy. When Legal asked "can you prove this data was classified as confidential?" the answer was no.
**Fix:** Playbook should reference the data classification policy (or flag its absence as a critical prerequisite). Add a section: "Prerequisites — the following organizational policies must exist for this playbook to be effective." Recommend post-incident action: implement a data classification framework.

## Gap 6 — Employee Communication Monitoring Authorization Undefined

**Where it broke:** Inject 4 — SOC analyst reviewed emails without documented authorization
**Problem:** The SOC analyst accessed Marcus's email to identify forwarded messages but no one documented who authorized this. Depending on jurisdiction, unauthorized email monitoring can violate wiretap laws and make evidence inadmissible.
**Fix:** Add to the insider threat section: "Employee communication review requires written authorization from [General Counsel / HR Director]. Document the authorization in the incident ticket before accessing any employee communications. Reference: company Acceptable Use Policy and Electronic Monitoring Policy."

## Gap 7 — No Pre-Authorized Forensic Procedures

**Where it broke:** Inject 4 — laptop imaging delayed waiting for Legal
**Problem:** Forensic imaging of a corporate-owned laptop during a SEV-1 incident required ad-hoc Legal approval. This delayed evidence collection by hours.
**Fix:** Establish standing Legal authorization for standard forensic procedures on corporate-owned assets during SEV-1/2 incidents. Document this in the playbook's Preparation section. Specific pre-authorized actions: disk imaging, memory capture, log export, account audit — all on corporate-owned assets. Personal devices still require specific authorization.

## Gap 8 — No Investigation OPSEC Protocol

**Where it broke:** Inject 5 — manager overheard IT discussing the investigation
**Problem:** The playbook says "maintain confidentiality" and "limit knowledge to essential personnel" but provides no operational guidance. No need-to-know list, no code name convention, no explicit instructions to involved teams about not discussing the investigation.
**Fix:** Add an OPSEC section for sensitive investigations: mandatory need-to-know list maintained by IR Lead, code name assigned at incident creation, explicit verbal instruction to every involved person ("do not discuss outside this channel"), investigation communications restricted to a private channel (not #incident-response), manager notification decision documented by IR Lead.

## Gap 9 — Privacy Officer / DPO Missing from IRT

**Where it broke:** Inject 5 — CISO asks about breach notification, no one owns the answer
**Problem:** The IRT roles table and contact roster have no Privacy Officer or Data Protection Officer. Breach notification is a regulatory compliance function requiring specific legal knowledge (state laws, GDPR Article 33, contractual obligations). Without this role, nobody owns the determination.
**Fix:** Add Privacy Officer / DPO to the IRT roles table with responsibility: "Breach notification assessment, regulatory compliance, data subject rights." Add to contact roster with 24/7 contact for SEV-1 incidents involving regulated data.

## Gap 10 — No Covert-to-Overt Investigation Transition

**Where it broke:** Inject 5 — investigation blown, analyst unsure how to shift approach
**Problem:** The playbook's insider threat section emphasizes covert investigation ("do not confront," "gather evidence first") but never addresses what happens when cover is blown. The analyst didn't recognize that once Marcus was tipped off, the entire containment strategy needed to change — from silent monitoring to immediate lockdown.
**Fix:** Add a "Covert Investigation Compromise" section: "If the subject becomes aware of the investigation: immediately escalate to IR Lead, transition to overt containment (full account disable, laptop recall), coordinate emergency HR action, preserve all evidence immediately, assume the subject will attempt to destroy evidence on personal devices."

---

## Recommendations Beyond the Playbook

These gaps require organizational action, not just playbook updates:

1. **Implement a data classification policy** — define Restricted, Confidential, Internal, Public. Map data types to classifications. This is a prerequisite for any insider threat legal action.
2. **Implement HR-triggered access reviews** — when an employee resigns, HR notifies Security. Security reduces access to role-minimum and enables enhanced monitoring before the employee is even aware.
3. **Deploy UEBA** — User and Entity Behavior Analytics would have baselined Marcus's normal behavior and flagged anomalies within the first 2-3 days, not Day 12.
4. **DLP in block mode for departing employees** — once resignation is filed, DLP policy shifts from "alert" to "block" for sensitive data transfers.
5. **Mandatory NDA review** — Legal should review NDAs annually and ensure they reference the data classification policy with specific data categories, not vague "proprietary information" language.
