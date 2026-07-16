# 🛡️ Incident Response Playbook Lab

**NIST-aligned IR playbook, stress-tested with a tabletop exercise, then revised based on findings.**

This isn't a theoretical document — it's a playbook that was written, tested against a realistic insider threat scenario, broke in 10 places, and was rebuilt stronger. The v1 → tabletop → gaps → v2 progression demonstrates the NIST post-incident improvement cycle in practice.

---

## What This Demonstrates

1. **Wrote a comprehensive IR playbook** aligned to NIST SP 800-61 Rev. 2 covering all four phases: Preparation, Detection & Analysis, Containment/Eradication/Recovery, and Post-Incident Activity.

2. **Designed and ran a tabletop exercise** — an insider threat scenario where a departing DBA systematically exfiltrates customer PII, trade secrets, and engineering files to a competitor over 12 days. Six escalating injects tested triage, containment, evidence handling, communication, and regulatory compliance.

3. **Identified 10 gaps** across process, authority, communication, legal, and organizational categories.

4. **Revised the playbook (v2)** with targeted fixes: contextual severity modifiers, containment authority matrix, OPSEC protocol, covert-to-overt transition guidance, pre-authorized forensic procedures, and more.

---

## The Gaps (Summary)

| # | Gap | Category |
|---|-----|----------|
| 1 | Severity matrix lacks contextual factors | Process |
| 2 | SEV-1 criteria ambiguity | Process |
| 3 | No after-hours escalation for HR/Legal | Availability |
| 4 | No containment authority matrix | Authority |
| 5 | No data classification policy referenced | Organizational |
| 6 | Employee email review authorization undefined | Legal |
| 7 | No pre-authorized forensic procedures | Process |
| 8 | No investigation OPSEC protocol | Communication |
| 9 | Privacy Officer/DPO missing from IRT | Roles |
| 10 | No covert-to-overt transition guidance | Process |

📄 [Full gap analysis →](tabletop/gaps-identified.md)

---

## Project Structure

```
ir-playbook/
├── README.md
├── playbook/
│   ├── IR-Playbook-v1.md              ← Original playbook (before tabletop)
│   └── IR-Playbook-v2.md              ← Revised — all 10 gaps addressed
├── tabletop/
│   ├── scenario-insider-threat.md     ← 6-inject tabletop exercise
│   ├── exercise-log.md               ← Decisions made at each inject
│   └── gaps-identified.md            ← What broke and why
└── templates/
    ├── incident-ticket.md            ← SOC incident ticket with evidence chain
    ├── escalation-checklist.md       ← When and how to escalate
    └── comms-template.md             ← Notification templates (internal + external)
```

---

## How to Read This Repo

**Start here:** Read the [v1 playbook](playbook/IR-Playbook-v1.md) — this is what was tested.

**Then:** Walk through the [tabletop scenario](tabletop/scenario-insider-threat.md) and the [exercise log](tabletop/exercise-log.md) — see what happened and what broke.

**Then:** Read the [gaps analysis](tabletop/gaps-identified.md) — the 10 specific failures identified.

**Finally:** Read the [v2 playbook](playbook/IR-Playbook-v2.md) — every `[v2]` tag marks a fix. Compare to v1 to see exactly what changed and why.

---

## Skills Demonstrated

- **NIST SP 800-61 Rev. 2** — all four IR phases applied and operationalized
- **Incident Response Process** — triage, severity classification, containment, evidence preservation, eradication, recovery, lessons learned
- **Insider Threat Handling** — covert investigation, evidence defensibility, HR/Legal coordination, OPSEC
- **Tabletop Exercise Design** — realistic scenario with escalating injects, decision forcing, gap identification
- **Regulatory Awareness** — GDPR Article 33 notification timelines, state breach notification laws, HIPAA
- **Process Improvement** — iterative playbook refinement based on exercise findings

---

## Certifications & Context

Built as part of a hands-on security lab portfolio. Related credentials:
- CompTIA Security+
- ISC² Certified in Cybersecurity (CC)
- Blue Team Level 1 (Security Blue Team)
