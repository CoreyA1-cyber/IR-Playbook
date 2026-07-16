# Incident Response Playbook v1.0

**Organization:** Acme Corporation
**Author:** Corey Armstrong — SOC Analyst
**Framework:** NIST SP 800-61 Rev. 2 (Computer Security Incident Handling Guide)
**Last Updated:** July 2026
**Classification:** Internal Use Only

---

## Table of Contents

1. [Purpose & Scope](#1-purpose--scope)
2. [Incident Severity Classification](#2-incident-severity-classification)
3. [Phase 1 — Preparation](#3-phase-1--preparation)
4. [Phase 2 — Detection & Analysis](#4-phase-2--detection--analysis)
5. [Phase 3 — Containment, Eradication & Recovery](#5-phase-3--containment-eradication--recovery)
6. [Phase 4 — Post-Incident Activity](#6-phase-4--post-incident-activity)
7. [Incident Type: Insider Threat / Data Exfiltration](#7-incident-type-insider-threat--data-exfiltration)
8. [Contact Roster](#8-contact-roster)

---

## 1. Purpose & Scope

This playbook establishes a structured, repeatable process for identifying, responding to, and recovering from cybersecurity incidents. It aligns with NIST SP 800-61 Rev. 2 and applies to all information systems, users, and data assets within Acme Corporation.

**In Scope:**

- All corporate endpoints, servers, and network infrastructure
- Cloud environments (AWS, Azure, GCP)
- SaaS applications (Microsoft 365, Salesforce, Slack)
- Corporate and BYOD devices accessing company resources

**Out of Scope:**

- Physical security incidents (handled by Facilities)
- Safety/environmental incidents (handled by EHS)

---

## 2. Incident Severity Classification

Every incident gets a severity level at triage. This determines response speed, who gets involved, and escalation requirements.

| Severity | Definition | Response Time | Examples |
|----------|-----------|---------------|----------|
| **SEV-1 / Critical** | Active data breach, business operations halted, or regulatory exposure | 15 minutes | Ransomware encrypting production systems, confirmed exfiltration of PII/PHI, active attacker in the network |
| **SEV-2 / High** | Confirmed compromise with potential for significant impact, or large-scale phishing campaign | 1 hour | Compromised admin credentials, malware on multiple endpoints, insider threat indicators |
| **SEV-3 / Medium** | Suspicious activity requiring investigation, single-system compromise | 4 hours | Single endpoint malware, unauthorized access attempt, DLP policy violation |
| **SEV-4 / Low** | Minor policy violation or informational alert | 24 hours | Failed login brute force (blocked), minor policy exception, phishing email reported (no click) |

**Escalation Rule:** If at any point an incident's scope or impact grows beyond its initial severity, re-classify immediately and notify the appropriate tier.

---

## 3. Phase 1 — Preparation

Preparation is everything you do BEFORE an incident. If this phase is weak, every other phase falls apart under pressure.

### 3.1 Incident Response Team (IRT) Roles

| Role | Responsibility | Typical Title |
|------|---------------|---------------|
| **IR Lead** | Owns the incident end-to-end. Makes containment decisions, coordinates resources, drives the timeline. | Senior SOC Analyst / IR Manager |
| **SOC Analyst (Tier 1)** | Initial triage, alert validation, evidence collection, log pulls. | SOC Analyst |
| **SOC Analyst (Tier 2/3)** | Deep-dive forensics, malware analysis, threat hunting across the environment. | Senior Analyst / Threat Hunter |
| **IT Operations** | Executes containment actions — network isolation, account disablement, system restores. | Sysadmin / Network Engineer |
| **Management** | Authorizes business-impacting decisions (shutting down systems, notifying customers). | CISO / IT Director |

### 3.2 Tools & Access

Ensure the following are operational and accessible to the IRT **before** an incident:

| Tool | Purpose | Access Required |
|------|---------|----------------|
| SIEM (Splunk) | Log aggregation, correlation, alerting | SOC analysts — search & alert creation |
| EDR (CrowdStrike) | Endpoint visibility, isolation, forensic timeline | SOC analysts — full response actions |
| DLP Platform | Data movement monitoring, policy alerts | SOC analysts — read; DLP team — write |
| Ticketing (ServiceNow) | Incident tracking, chain of custody, SLA timers | All IRT members |
| Network Monitoring (Zeek/Suricata) | Network traffic analysis, anomaly detection | SOC Tier 2+ |
| Forensic Workstation | Disk imaging, memory capture, evidence preservation | IR Lead / Tier 3 only |

### 3.3 Communication Plan

| Audience | Channel | When |
|----------|---------|------|
| SOC Team | Slack #incident-response | Immediately on detection |
| IR Lead | Phone call + Slack | Within response time SLA |
| Management / CISO | Email + phone | SEV-1/SEV-2 within 30 min |
| Legal | Email | When data breach is suspected |
| External (customers, regulators) | Formal written notice | Per legal guidance only |

### 3.4 Documentation Requirements

Every incident must have:

- **Incident ticket** (opened immediately at triage)
- **Evidence log** (every artifact collected, timestamped, hashed)
- **Timeline** (chronological record of attacker actions AND response actions)
- **Decision log** (who decided what, when, and why)

---

## 4. Phase 2 — Detection & Analysis

This is where you determine: is this real, how bad is it, and what are we dealing with?

### 4.1 Detection Sources

Incidents can be detected through:

- **Automated alerts** — SIEM correlation rules, EDR detections, DLP violations, IDS/IPS
- **User reports** — Phishing reports, "something looks wrong" tickets
- **Threat intelligence** — IOC feeds matching internal telemetry
- **Third-party notification** — Law enforcement, vendor, or partner informing you of compromise
- **Proactive hunting** — Threat hunters identify activity that evaded automated detection

### 4.2 Initial Triage Process

When an alert fires or a report comes in:

**Step 1 — Validate the alert.**
Is this a true positive or a false positive? Check:
- Does the alert have context? (source IP, user, hostname, timestamp)
- Can you reproduce or corroborate it with a second data source?
- Is this a known false-positive pattern?

**Step 2 — Determine scope.**
Is this one endpoint, one user, or potentially broader?
- Search SIEM for related activity (same source IP, same user, same malware hash)
- Check EDR for lateral movement indicators
- Review authentication logs for the affected account

**Step 3 — Classify severity.**
Use the severity matrix above. When in doubt, classify UP — it's easier to downgrade than to explain why you under-responded.

**Step 4 — Open the incident ticket.**
Use the [Incident Ticket Template](../templates/incident-ticket.md). This becomes the single source of truth.

### 4.3 Key Analysis Questions

For every incident, the analyst must answer:

1. **What happened?** — What is the nature of the activity?
2. **When did it start?** — What's the earliest evidence of this activity?
3. **What systems are affected?** — Endpoints, servers, cloud resources, accounts?
4. **What data is at risk?** — Is sensitive/regulated data involved (PII, PHI, financial)?
5. **Is the activity ongoing?** — Is the attacker still active right now?
6. **How did they get in?** — Initial access vector (phishing, vuln exploit, credential stuffing, insider)?

---

## 5. Phase 3 — Containment, Eradication & Recovery

### 5.1 Containment Strategies

Containment has two stages — short-term (stop the bleeding NOW) and long-term (prevent re-compromise while you investigate).

**Short-Term Containment (immediate — minutes):**

| Action | When to Use | Who Executes |
|--------|------------|-------------|
| Network isolation (EDR) | Confirmed malware, active C2 communication | SOC Analyst |
| Disable user account | Compromised credentials, insider threat | IT Operations (SOC requests) |
| Block IP/domain at firewall | Known malicious infrastructure | Network Engineer (SOC requests) |
| Disable VPN access | Compromised remote user | IT Operations |
| Kill malicious process | Active malware execution | SOC Analyst via EDR |

**Long-Term Containment (hours — while investigation continues):**

- Move affected systems to isolated VLAN (can still be analyzed)
- Apply emergency firewall rules
- Force password resets for affected accounts + related accounts
- Enable enhanced monitoring on adjacent systems
- Revoke active sessions and OAuth tokens

### 5.2 Evidence Preservation

**CRITICAL: Preserve BEFORE you eradicate.** Once you wipe a system, the evidence is gone.

Evidence collection priority:
1. **Volatile data first** — RAM, running processes, active network connections (disappears on reboot)
2. **Disk images** — Full forensic image before remediation
3. **Log exports** — SIEM, EDR, authentication, DNS, proxy, firewall
4. **Screenshots** — Visual documentation of alerts, dashboards, attacker activity

All evidence must be:
- Hashed (SHA-256) at time of collection
- Stored in a write-protected location
- Logged in the evidence chain of custody

### 5.3 Eradication

Remove the threat completely:

- Remove malware/backdoors from affected systems
- Close the initial access vector (patch vuln, disable compromised account)
- Remove attacker persistence mechanisms (scheduled tasks, registry keys, cron jobs)
- Scan environment for additional indicators (same malware hash, same C2 domain)

### 5.4 Recovery

Restore normal operations:

- Rebuild compromised systems from known-good images (don't just "clean" them)
- Restore data from verified clean backups
- Re-enable accounts with new credentials + MFA enforcement
- Monitor restored systems closely for 72 hours for re-compromise
- Gradually remove emergency firewall rules as confidence grows

---

## 6. Phase 4 — Post-Incident Activity

This is the phase most teams skip — and it's the most valuable.

### 6.1 Lessons Learned Meeting

Hold within **5 business days** of incident closure. Attendees: everyone involved in the response.

**Questions to answer:**

1. What happened, and what was the root cause?
2. How was the incident detected? Could we have caught it earlier?
3. What went well in the response?
4. What didn't work or was missing?
5. Were there any communication breakdowns?
6. What changes should we make to prevent recurrence?

### 6.2 Metrics to Track

| Metric | Definition |
|--------|-----------|
| **MTTD (Mean Time to Detect)** | Time from incident start to detection |
| **MTTR (Mean Time to Respond)** | Time from detection to containment |
| **MTTC (Mean Time to Contain)** | Time from first response action to full containment |
| **Incident Volume** | Total incidents per month, by type and severity |
| **False Positive Rate** | Percentage of alerts that were false positives |

### 6.3 Playbook Updates

After every significant incident, review and update:
- This playbook (you're reading a living document)
- Detection rules (did our SIEM catch this? If not, write a new rule)
- Runbooks for specific incident types
- Training materials for analysts

---

## 7. Incident Type: Insider Threat / Data Exfiltration

Insider threat requires a different approach than external attacks. The "attacker" has legitimate access, knows the environment, and may be actively trying to avoid detection.

### 7.1 Common Indicators

- Unusual data access patterns (volume, time, sensitivity level)
- Large file downloads or transfers to personal email/cloud storage
- Access to systems or data outside the user's normal role
- Use of unauthorized USB devices or personal cloud sync tools
- Employee recently gave notice, was placed on PIP, or expressed grievances
- VPN connections from unusual locations or at unusual times
- DLP alerts for sensitive data leaving the network

### 7.2 Detection Approach

- **DLP alerts** — monitor for large data transfers, sensitive file types, external destinations
- **UEBA (User & Entity Behavior Analytics)** — baseline normal behavior, alert on anomalies
- **Access log review** — compare current access patterns to historical baseline
- **HR correlation** — cross-reference alerts with HR events (resignation, PIP, termination scheduled)

### 7.3 Response Considerations

Insider threat response differs from external threats in critical ways:

- **Do NOT immediately confront the user.** Gather evidence first. Premature confrontation gives them time to cover tracks or accelerate exfiltration.
- **Involve HR early.** This is an employee matter as much as a security matter.
- **Involve Legal early.** Evidence handling must be defensible. Monitoring an employee has legal boundaries.
- **Maintain confidentiality.** Limit knowledge of the investigation to essential personnel only.
- **Document everything.** Assume this goes to court or arbitration.

### 7.4 Containment (Insider-Specific)

| Action | Consideration |
|--------|--------------|
| Reduce access (don't fully disable yet) | Avoid tipping off the subject — remove sensitive data access while maintaining normal login |
| Increase monitoring | Enhanced logging on their account, DLP in block mode, screen recording if legally approved |
| Preserve evidence | Image their workstation, export email archive, pull cloud storage logs |
| Coordinate with HR | Align timing — security containment and HR action should happen simultaneously |

---

## 8. Contact Roster

| Role | Name | Phone | Email | Availability |
|------|------|-------|-------|-------------|
| IR Lead | Jane Mitchell | (555) 100-2001 | j.mitchell@acmecorp.com | 24/7 on-call |
| SOC Manager | Robert Chen | (555) 100-2002 | r.chen@acmecorp.com | Business hours |
| CISO | Diane Kowalski | (555) 100-2003 | d.kowalski@acmecorp.com | SEV-1/2 escalation |
| IT Operations Lead | Mark Patel | (555) 100-2004 | m.patel@acmecorp.com | 24/7 on-call |
| General Counsel | Sarah Whitfield | (555) 100-2005 | s.whitfield@acmecorp.com | Business hours |
| HR Director | Thomas Grant | (555) 100-2006 | t.grant@acmecorp.com | Business hours |

---

*This playbook is a living document. Updates are made after every significant incident and reviewed quarterly.*
