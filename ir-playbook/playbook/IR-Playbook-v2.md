# Incident Response Playbook v2.0

**Organization:** Acme Corporation
**Author:** Corey Armstrong — SOC Analyst
**Framework:** NIST SP 800-61 Rev. 2 (Computer Security Incident Handling Guide)
**Last Updated:** July 2026
**Classification:** Internal Use Only
**Change Log:** v2.0 — revised after tabletop exercise (insider threat scenario). 10 gaps identified and addressed. See [gaps-identified.md](../tabletop/gaps-identified.md) for full details.

---

## Table of Contents

1. [Purpose & Scope](#1-purpose--scope)
2. [Prerequisites](#2-prerequisites)
3. [Incident Severity Classification](#3-incident-severity-classification)
4. [Phase 1 — Preparation](#4-phase-1--preparation)
5. [Phase 2 — Detection & Analysis](#5-phase-2--detection--analysis)
6. [Phase 3 — Containment, Eradication & Recovery](#6-phase-3--containment-eradication--recovery)
7. [Phase 4 — Post-Incident Activity](#7-phase-4--post-incident-activity)
8. [Incident Type: Insider Threat / Data Exfiltration](#8-incident-type-insider-threat--data-exfiltration)
9. [Contact Roster](#9-contact-roster)

---

## 1. Purpose & Scope

This playbook establishes a structured, repeatable process for identifying, responding to, and recovering from cybersecurity incidents. It aligns with NIST SP 800-61 Rev. 2 and applies to all information systems, users, and data assets within Acme Corporation.

**In Scope:**

- All corporate endpoints, servers, and network infrastructure
- Cloud environments (AWS, Azure, GCP)
- SaaS applications (Microsoft 365, Salesforce, Slack)
- Corporate and BYOD devices accessing company resources

**Out of Scope:**

- Physical security incidents (handled by Facilities — coordinate with Security when overlap exists)
- Safety/environmental incidents (handled by EHS)

---

## 2. Prerequisites

> **[v2 — NEW SECTION]** Added after tabletop revealed organizational dependencies.

The following organizational policies and capabilities must be in place for this playbook to function effectively. If any are missing, flag to the CISO as a critical gap.

| Prerequisite | Status | Owner |
|-------------|--------|-------|
| **Data Classification Policy** — defines Restricted, Confidential, Internal, Public categories with specific data type mappings | Required | CISO / Privacy Officer |
| **Acceptable Use Policy** — defines permitted use of company systems, data handling, personal device restrictions | Required | HR / Legal |
| **Electronic Monitoring Policy** — establishes the organization's right to monitor employee activity on corporate systems, signed by all employees | Required | Legal / HR |
| **Employee NDA** — references the data classification policy with specific categories (not just "proprietary information") | Required | Legal |
| **HR Security Notification Process** — HR notifies Security within 24 hours of any resignation, termination, or PIP action | Required | HR |
| **UEBA Capability** — user behavior baselining and anomaly detection | Recommended | Security Engineering |

---

## 3. Incident Severity Classification

Every incident gets a severity level at triage. This determines response speed, who gets involved, and escalation requirements.

**ANY single criterion is sufficient to assign that severity level. When in doubt, classify UP — downgrading is easier than explaining under-response.**

| Severity | Definition | Response Time | Examples |
|----------|-----------|---------------|----------|
| **SEV-1 / Critical** | Active data breach, business operations halted, OR regulatory exposure | 15 minutes | Ransomware encrypting production, confirmed exfiltration of PII/PHI, active attacker in network |
| **SEV-2 / High** | Confirmed compromise with potential for significant impact, or large-scale phishing campaign | 1 hour | Compromised admin credentials, malware on multiple endpoints, insider threat indicators |
| **SEV-3 / Medium** | Suspicious activity requiring investigation, single-system compromise | 4 hours | Single endpoint malware, unauthorized access attempt, DLP policy violation (no aggravating context) |
| **SEV-4 / Low** | Minor policy violation or informational alert | 24 hours | Failed brute force (blocked), minor policy exception, phishing reported (no click) |

### 3.1 Contextual Severity Modifiers

> **[v2 — NEW]** Added after tabletop showed analysts under-classifying when context was critical.

The following contextual factors automatically escalate severity by at least one level, regardless of the initial alert type:

| Modifier | Effect | Rationale |
|----------|--------|-----------|
| **Departing employee** (resigned, terminated, PIP) | +1 severity | Elevated exfiltration risk, time-limited response window |
| **Privileged access** (admin, DBA, root) | +1 severity | Blast radius is larger with elevated permissions |
| **Regulated data involved** (PII, PHI, PCI, GDPR-covered) | +1 severity | Triggers notification obligations and legal exposure |
| **Competitor involvement** (subject leaving to competitor, third-party tip) | +1 severity | Indicates potential trade secret theft, not just policy violation |
| **Pattern of behavior** (multiple alerts or multi-day activity) | +1 severity | Single events can be mistakes; patterns indicate intent |

**Application:** A DLP alert (normally SEV-3) involving a departing employee (+1) with PII (+1) heading to a competitor (+1) = **SEV-1 minimum.** Modifiers do not stack beyond SEV-1.

---

## 4. Phase 1 — Preparation

### 4.1 Incident Response Team (IRT) Roles

| Role | Responsibility | Typical Title |
|------|---------------|---------------|
| **IR Lead** | Owns the incident end-to-end. Makes containment decisions, coordinates resources, drives the timeline. Maintains the need-to-know list for sensitive investigations. | Senior SOC Analyst / IR Manager |
| **SOC Analyst (Tier 1)** | Initial triage, alert validation, evidence collection, log pulls. | SOC Analyst |
| **SOC Analyst (Tier 2/3)** | Deep-dive forensics, malware analysis, threat hunting across the environment. | Senior Analyst / Threat Hunter |
| **IT Operations** | Executes containment actions — network isolation, account disablement, system restores. | Sysadmin / Network Engineer |
| **Management** | Authorizes business-impacting decisions (shutting down systems, notifying customers). | CISO / IT Director |
| **Privacy Officer / DPO** | Breach notification assessment, regulatory compliance (state laws, GDPR), data subject rights. Determines whether the incident is a reportable breach. | Privacy Officer / DPO |

> **[v2 — ADDED]** Privacy Officer / DPO role added after tabletop revealed no one owned breach notification determination.

### 4.2 Tools & Access

Ensure the following are operational and accessible to the IRT **before** an incident:

| Tool | Purpose | Access Required |
|------|---------|----------------|
| SIEM (Splunk) | Log aggregation, correlation, alerting | SOC analysts — search & alert creation |
| EDR (CrowdStrike) | Endpoint visibility, isolation, forensic timeline | SOC analysts — full response actions |
| DLP Platform (Netskope) | Data movement monitoring, policy alerts | SOC analysts — read; DLP team — write |
| Ticketing (ServiceNow) | Incident tracking, chain of custody, SLA timers | All IRT members |
| Network Monitoring (Zeek/Suricata) | Network traffic analysis, anomaly detection | SOC Tier 2+ |
| Forensic Workstation | Disk imaging, memory capture, evidence preservation | IR Lead / Tier 3 only |
| UEBA | User behavior baselining, anomaly detection | SOC Tier 2+ |

### 4.3 Communication Plan

| Audience | Channel | When | After-Hours Method |
|----------|---------|------|-------------------|
| SOC Team | Slack — private incident channel | Immediately on detection | Same |
| IR Lead | Phone call + Slack | Within response time SLA | Cell phone (see roster) |
| Management / CISO | Email + phone | SEV-1/SEV-2 within 30 min | Cell phone (see roster) |
| Legal | Email + phone | When data breach is suspected | Cell phone (see roster) |
| HR Director | Email + phone | When insider threat or employee involved | Cell phone (see roster) |
| Privacy Officer / DPO | Email + phone | When regulated data is involved (PII/PHI/PCI/GDPR) | Cell phone (see roster) |
| External (customers, regulators) | Formal written notice | Per Privacy Officer + Legal guidance only | N/A |

> **[v2 — UPDATED]** Added after-hours contact methods for all critical roles. Added Privacy Officer. After-hours escalation rule: if primary contact is unreachable within 30 minutes, escalate to their backup (see contact roster).

### 4.4 Documentation Requirements

Every incident must have:

- **Incident ticket** (opened immediately at triage)
- **Evidence log** (every artifact collected, timestamped, SHA-256 hashed)
- **Timeline** (chronological record of attacker actions AND response actions)
- **Decision log** (who decided what, when, and why — including triage rationale)

> **[v2 — UPDATED]** Added "including triage rationale" — tabletop showed initial triage decisions went undocumented.

### 4.5 Pre-Authorized Forensic Procedures

> **[v2 — NEW SECTION]** Added after tabletop showed evidence collection delayed by ad-hoc authorization requests.

The following forensic actions are **pre-authorized by General Counsel** for all SEV-1 and SEV-2 incidents involving corporate-owned assets. No per-incident legal approval is required:

| Action | Scope | Condition |
|--------|-------|-----------|
| Full disk image | Corporate-owned laptops, desktops, servers | SEV-1 or SEV-2 incident |
| Memory capture | Corporate-owned systems | SEV-1 or SEV-2 incident |
| Log export and preservation | All corporate systems (SIEM, EDR, SaaS audit logs, network) | Any severity |
| Account activity audit | Corporate accounts (email, cloud, VPN, SaaS) | SEV-1 or SEV-2, documented in ticket |

**Requires specific Legal authorization:**
- Accessing personal devices (even if used for work)
- Employee communication content review (email body, chat messages) — requires written authorization from General Counsel, documented in incident ticket BEFORE access
- Any monitoring beyond standard audit logs

> **[v2 — KEY FIX]** Employee communication review now requires explicit, documented Legal authorization. This was identified as a legal risk during the tabletop — unauthorized email review could violate wiretap laws and render evidence inadmissible.

### 4.6 Containment Authority Matrix

> **[v2 — NEW SECTION]** Added after tabletop showed analysts unsure who could authorize containment when HR/Legal were unavailable.

| Containment Action | Who Can Authorize | After-Hours Alternative |
|-------------------|------------------|----------------------|
| Block IP/domain at firewall | SOC Analyst (Tier 1+) | Same |
| Network isolate endpoint (EDR) | SOC Analyst (Tier 1+) | Same |
| Kill malicious process | SOC Analyst (Tier 1+) | Same |
| **Restrict data access (silent)** — remove specific permissions without disabling account | **IR Lead** | IR Lead on-call |
| **Enable DLP block mode** on specific user | **IR Lead** | IR Lead on-call |
| **Disable user account (overt)** | **IR Lead + HR coordination** | IR Lead + CISO if HR unreachable for 30 min |
| **Revoke VPN / remote access** | **IR Lead** | IR Lead on-call |
| **Force password reset** | IT Operations (SOC requests) | IT on-call |
| **Shut down production system** | **CISO only** | CISO cell phone |

---

## 5. Phase 2 — Detection & Analysis

### 5.1 Detection Sources

- **Automated alerts** — SIEM correlation rules, EDR detections, DLP violations, IDS/IPS, UEBA anomalies
- **User reports** — Phishing reports, "something looks wrong" tickets
- **Threat intelligence** — IOC feeds matching internal telemetry
- **Third-party notification** — Law enforcement, vendor, or partner informing you of compromise
- **Proactive hunting** — Threat hunters identify activity that evaded automated detection
- **HR notifications** — Employee resignation, termination, or PIP triggers a security review of access and recent activity

> **[v2 — ADDED]** HR notifications as a detection source — departing employee access review.

### 5.2 Initial Triage Process

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
Use the severity matrix AND the contextual modifiers. Apply modifiers for: departing employee, privileged access, regulated data, competitor involvement, pattern of behavior.

**Step 4 — Document triage rationale.**
In the incident ticket, record: what the alert was, what you checked, what severity you assigned and WHY (including which modifiers applied), and what your next steps are. This documentation starts NOW, not after the investigation.

> **[v2 — UPDATED]** Step 3 now references contextual modifiers. Step 4 added — triage rationale documentation.

**Step 5 — Open the incident ticket.**
Use the [Incident Ticket Template](../templates/incident-ticket.md). This becomes the single source of truth.

### 5.3 Key Analysis Questions

1. **What happened?** — What is the nature of the activity?
2. **When did it start?** — What's the earliest evidence of this activity?
3. **What systems are affected?** — Endpoints, servers, cloud resources, accounts?
4. **What data is at risk?** — Is sensitive/regulated data involved? Reference the data classification policy — what classification level is the affected data?
5. **Is the activity ongoing?** — Is the attacker/subject still active right now?
6. **How did they get in?** — Initial access vector (phishing, vuln exploit, credential stuffing, insider)?

> **[v2 — UPDATED]** Question 4 now references data classification policy.

---

## 6. Phase 3 — Containment, Eradication & Recovery

### 6.1 Containment Strategies

Containment has two stages — short-term (stop the bleeding NOW) and long-term (prevent re-compromise while you investigate).

**Refer to the Containment Authority Matrix (Section 4.6) for approval requirements.**

**Short-Term Containment (immediate — minutes):**

| Action | When to Use | Who Executes | Authorization |
|--------|------------|-------------|---------------|
| Network isolation (EDR) | Confirmed malware, active C2 | SOC Analyst | Self-authorized |
| Disable user account | Compromised credentials, overt insider threat | IT Ops (SOC requests) | IR Lead + HR |
| Block IP/domain at firewall | Known malicious infrastructure | Network Engineer | Self-authorized |
| Disable VPN access | Compromised remote user | IT Ops | IR Lead |
| Kill malicious process | Active malware execution | SOC Analyst via EDR | Self-authorized |
| **Silent access restriction** | Insider threat (covert phase) | IT Ops (SOC requests) | IR Lead |
| **DLP to block mode** | Active exfiltration by specific user | DLP team (SOC requests) | IR Lead |

> **[v2 — ADDED]** Silent access restriction and DLP block mode as containment options for insider threat. Authorization column added.

**Long-Term Containment (hours — while investigation continues):**

- Move affected systems to isolated VLAN (can still be analyzed)
- Apply emergency firewall rules
- Force password resets for affected accounts + related accounts
- Enable enhanced monitoring on adjacent systems
- Revoke active sessions and OAuth tokens

### 6.2 Evidence Preservation

**CRITICAL: Preserve BEFORE you eradicate.** Once you wipe a system, the evidence is gone.

Evidence collection priority:
1. **Volatile data first** — RAM, running processes, active network connections (disappears on reboot)
2. **Disk images** — Full forensic image before remediation (pre-authorized for SEV-1/2 on corporate assets — see Section 4.5)
3. **Log exports** — SIEM, EDR, authentication, DNS, proxy, firewall, SaaS audit logs
4. **SaaS audit logs** — Export AND hash immediately. These logs live in third-party systems and may rotate or be overwritten. Do not assume they will persist.
5. **Screenshots** — Visual documentation of alerts, dashboards, attacker activity

> **[v2 — ADDED]** SaaS audit log preservation explicitly called out — tabletop showed Salesforce logs were left in the live system.

All evidence must be:
- Hashed (SHA-256) at time of collection
- Stored in a write-protected location
- Logged in the evidence chain of custody
- Collected by authorized personnel with documented authorization (see Section 4.5)

### 6.3 Eradication

- Remove malware/backdoors from affected systems
- Close the initial access vector (patch vuln, disable compromised account)
- Remove attacker persistence mechanisms (scheduled tasks, registry keys, cron jobs)
- Scan environment for additional indicators (same malware hash, same C2 domain)

### 6.4 Recovery

- Rebuild compromised systems from known-good images (don't just "clean" them)
- Restore data from verified clean backups
- Re-enable accounts with new credentials + MFA enforcement
- Monitor restored systems closely for 72 hours for re-compromise
- Gradually remove emergency firewall rules as confidence grows

---

## 7. Phase 4 — Post-Incident Activity

### 7.1 Lessons Learned Meeting

Hold within **5 business days** of incident closure. Attendees: everyone involved in the response.

**Questions to answer:**

1. What happened, and what was the root cause?
2. How was the incident detected? Could we have caught it earlier?
3. What went well in the response?
4. What didn't work or was missing?
5. Were there any communication breakdowns?
6. What changes should we make to prevent recurrence?
7. Were there any authorization or escalation delays? Why?
8. Did the playbook provide adequate guidance, or were decisions made ad hoc?

> **[v2 — ADDED]** Questions 7 and 8 — specifically addressing authorization gaps and playbook adequacy.

### 7.2 Metrics to Track

| Metric | Definition |
|--------|-----------|
| **MTTD (Mean Time to Detect)** | Time from incident start to detection |
| **MTTR (Mean Time to Respond)** | Time from detection to containment |
| **MTTC (Mean Time to Contain)** | Time from first response action to full containment |
| **Incident Volume** | Total incidents per month, by type and severity |
| **False Positive Rate** | Percentage of alerts that were false positives |
| **Detection Gap** | Time between first malicious activity and first alert (target: < 48 hours) |

> **[v2 — ADDED]** Detection Gap metric — tabletop exposed a 12-day gap between first exfiltration and first alert.

### 7.3 Playbook Updates

After every significant incident, review and update:
- This playbook (you're reading a living document)
- Detection rules (did our SIEM catch this? If not, write a new rule)
- Runbooks for specific incident types
- Training materials for analysts

---

## 8. Incident Type: Insider Threat / Data Exfiltration

### 8.1 Common Indicators

- Unusual data access patterns (volume, time, sensitivity level)
- Large file downloads or transfers to personal email/cloud storage
- Access to systems or data outside the user's normal role
- Use of unauthorized USB devices or personal cloud sync tools
- Employee recently gave notice, was placed on PIP, or expressed grievances
- VPN connections from unusual locations or at unusual times
- DLP alerts for sensitive data leaving the network
- Installation of file sync/transfer tools (FreeFileSync, WinSCP, rclone)

### 8.2 Detection Approach

- **DLP alerts** — monitor for large data transfers, sensitive file types, external destinations
- **UEBA** — baseline normal behavior, alert on anomalies (volume, time, access patterns)
- **Access log review** — compare current access patterns to historical baseline
- **HR correlation** — cross-reference alerts with HR events (resignation, PIP, termination)
- **HR-triggered review** — when HR notifies Security of a departure, proactively review the past 30 days of activity before the employee is aware of any investigation

> **[v2 — ADDED]** HR-triggered proactive review — don't wait for alerts, review departing employee activity automatically.

### 8.3 Response Considerations

Insider threat response differs from external threats in critical ways:

- **Do NOT immediately confront the subject.** Gather evidence first. Premature confrontation gives them time to cover tracks or accelerate exfiltration.
- **Involve HR early.** This is an employee matter as much as a security matter.
- **Involve Legal early.** Evidence handling must be defensible. Monitoring an employee has legal boundaries.
- **Involve the Privacy Officer** if regulated data (PII/PHI/PCI) is involved — breach notification clock may start at time of awareness.
- **Maintain strict OPSEC** (see Section 8.6).
- **Document everything.** Assume this goes to court or arbitration.
- **Reference the data classification policy** — you need to prove that exfiltrated data was formally classified as Confidential or Restricted.

> **[v2 — ADDED]** Privacy Officer involvement, OPSEC reference, data classification reference.

### 8.4 Containment (Insider-Specific)

| Action | Consideration | Authorization |
|--------|--------------|---------------|
| **Silent access restriction** — remove sensitive data access while maintaining normal login | Preferred first step — stops data flow without alerting subject | IR Lead |
| **DLP to block mode** — shift from alerting to blocking on the subject's account | Stops exfiltration at the network boundary | IR Lead |
| Increase monitoring — enhanced logging, screen recording if legally approved | Builds evidence while maintaining covert posture | IR Lead + Legal for screen recording |
| Preserve evidence — image workstation, export email archive, pull cloud storage logs | Do this BEFORE any overt action (pre-authorized for corporate assets, see Section 4.5) | Pre-authorized (SEV-1/2) |
| Coordinate with HR — align timing of security containment and HR action | Security and HR act simultaneously when going overt | IR Lead + HR Director |

### 8.5 Covert-to-Overt Transition

> **[v2 — NEW SECTION]** Added after tabletop showed no guidance for when cover is blown.

An insider threat investigation begins in **covert mode** — the subject doesn't know they're being investigated. This phase ends when:

- Sufficient evidence is collected and HR/Legal are ready to act
- The subject becomes aware of the investigation (cover is blown)
- The risk of continued access outweighs the intelligence value of monitoring

**When transitioning to overt mode:**

1. **Immediately escalate to IR Lead** — the response posture changes completely
2. **Disable the subject's account** — covert monitoring is no longer valuable; prevent further access
3. **Image the corporate laptop immediately** — assume the subject will attempt to delete evidence
4. **Coordinate emergency HR action** — termination meeting or administrative leave
5. **Assume destruction on personal devices** — the subject will likely wipe personal Google Drive, email, USB. Notify Legal for preservation demands to third-party providers (Google, ISP)
6. **Send Legal preservation letter** to the subject and their new employer (if competitor)
7. **Update the incident ticket** with the reason for transition and all actions taken

### 8.6 Investigation OPSEC Protocol

> **[v2 — NEW SECTION]** Added after tabletop showed the investigation was compromised by a hallway conversation.

For all insider threat and sensitive investigations:

**Need-to-Know List:**
- IR Lead creates and maintains a written need-to-know list at incident creation
- Only individuals on this list may be informed of the investigation
- Every person added receives an explicit verbal instruction: "Do not discuss this investigation with anyone not on this list, in any setting, including hallways, break rooms, or informal conversations"
- Documented in the incident ticket

**Communication:**
- Create a private Slack channel (not #incident-response) with a non-descriptive name
- All investigation discussion happens in this channel only
- No investigation details in general IT channels, tickets visible to non-IRT staff, or email chains that include non-essential personnel

**Subject's Manager:**
- The IR Lead decides whether the subject's manager is briefed or excluded
- If briefed: explicit instruction to maintain normal behavior, do not approach the subject, do not change assignments or access
- If excluded: the manager learns about the incident only at the point of HR action
- This decision is documented in the incident ticket with rationale

---

## 9. Contact Roster

| Role | Name | Office Phone | Cell Phone | Email | Availability |
|------|------|-------------|------------|-------|-------------|
| IR Lead | Jane Mitchell | (555) 100-2001 | (555) 200-2001 | j.mitchell@acmecorp.com | 24/7 on-call |
| SOC Manager | Robert Chen | (555) 100-2002 | (555) 200-2002 | r.chen@acmecorp.com | 24/7 on-call |
| CISO | Diane Kowalski | (555) 100-2003 | (555) 200-2003 | d.kowalski@acmecorp.com | 24/7 for SEV-1/2 |
| IT Operations Lead | Mark Patel | (555) 100-2004 | (555) 200-2004 | m.patel@acmecorp.com | 24/7 on-call |
| General Counsel | Sarah Whitfield | (555) 100-2005 | (555) 200-2005 | s.whitfield@acmecorp.com | 24/7 for SEV-1/2 |
| HR Director | Thomas Grant | (555) 100-2006 | (555) 200-2006 | t.grant@acmecorp.com | 24/7 for SEV-1/2 |
| Privacy Officer / DPO | Amy Reeves | (555) 100-2007 | (555) 200-2007 | a.reeves@acmecorp.com | 24/7 for SEV-1/2 with regulated data |
| HR Backup | Lisa Moreno | (555) 100-2008 | (555) 200-2008 | l.moreno@acmecorp.com | Backup if HR Director unreachable |
| Legal Backup | David Park | (555) 100-2009 | (555) 200-2009 | d.park@acmecorp.com | Backup if General Counsel unreachable |

> **[v2 — UPDATED]** Added cell phones for all critical roles. Added Privacy Officer. Added backup contacts for HR and Legal. Changed HR and Legal to 24/7 for SEV-1/2 incidents. Added after-hours escalation rule below.

**After-Hours Escalation Rule:** If a primary contact is unreachable within 30 minutes during a SEV-1 or SEV-2 incident, escalate to their designated backup. If the backup is also unreachable within 15 minutes, escalate to the CISO for emergency authorization.

---

*This playbook is a living document. Updates are made after every significant incident and reviewed quarterly. All changes are logged with rationale.*
