# Tabletop Exercise: Insider Threat — Data Exfiltration

**Exercise Type:** Discussion-based tabletop
**Scenario:** Insider threat / data exfiltration by a departing employee
**Duration:** ~90 minutes (6 injects, 15 min each)
**Framework Reference:** NIST SP 800-61 Rev. 2

---

## Exercise Objectives

1. Validate the IR playbook's insider threat procedures
2. Test coordination between Security, HR, Legal, and IT Operations
3. Identify gaps in detection, containment, and evidence handling
4. Practice decision-making under ambiguity

---

## Scenario Background

**Organization:** Acme Corporation — mid-size SaaS company, 800 employees
**Environment:**
- Microsoft 365 (email, OneDrive, SharePoint)
- AWS (production infrastructure)
- Salesforce (CRM — customer data)
- Splunk (SIEM)
- CrowdStrike (EDR)
- Netskope (CASB/DLP)

**The Subject:**
- **Marcus Webb** — Senior Database Administrator, 4 years at Acme
- Has elevated access to production databases (customer PII, financial records)
- Gave his two-week notice 8 days ago — last day is next Friday
- Accepted a position at a direct competitor
- Access has NOT been modified since resignation — standard practice is to wait until last day
- No prior HR issues, good performance reviews

---

## Inject 1 — The Alert (Detection & Analysis)

**Time: Tuesday, 2:17 PM**

The SOC receives an automated DLP alert from Netskope:

```
ALERT: DLP Policy Violation — High Severity
User:        marcus.webb@acmecorp.com
Action:      Upload to personal Google Drive
File:        Q3-Customer-Export-Full.csv (247 MB)
Records:     ~185,000 rows
Sensitivity: PII (names, emails, phone numbers, billing addresses)
Source:      Salesforce bulk data export
Timestamp:   2:14 PM EDT
```

This is the first DLP alert for Marcus. No prior policy violations on file.

**Discussion Questions:**

1. Based on the playbook, what severity level does this get?
2. Who gets notified first?
3. What is your immediate next step — do you contact Marcus? Why or why not?
4. What additional data sources do you pull to validate this alert?
5. Is this enough to open a formal incident? What's your threshold?

---

## Inject 2 — Scope Expands (Deeper Analysis)

**Time: Tuesday, 3:45 PM**

The SOC analyst pulls Marcus's activity logs from the past 14 days (since his resignation). Findings:

**Salesforce Audit Log:**
- 12 days ago: Ran a report pulling all active customer records (first time ever running this report)
- 8 days ago: Exported "Enterprise Tier Accounts" contact list (42 MB)
- 5 days ago: Exported full product pricing matrix (internal, not customer-facing)
- Today: Bulk export of 185,000 customer records (the DLP alert)

**Microsoft 365 Activity:**
- 6 days ago: Forwarded 14 emails to personal Gmail (marcus.webb.personal@gmail.com)
  - Subjects include: "Q4 Product Roadmap," "Enterprise Pricing Update," "Board Deck — Confidential"
- 3 days ago: Downloaded 340 files from SharePoint "Engineering" folder to local machine
- 1 day ago: Synced OneDrive → personal USB drive (Netskope logged the device but did NOT block)

**VPN Log:**
- 4 days ago: VPN login at 11:47 PM (Marcus's normal hours are 8 AM – 5 PM)
- Same session: Accessed production database admin console for 2 hours

**CrowdStrike EDR:**
- 3 days ago: Installed "FreeFileSync" (file synchronization tool) on corporate laptop
- No malware detected

**Discussion Questions:**

1. How does this change your severity classification?
2. The activity spans 12 days — what does that tell you about your detection gap?
3. Which of these activities is the most concerning and why?
4. Do you have enough to involve HR and Legal now? What's the trigger?
5. Marcus is still working normally — what do you do about his access RIGHT NOW?

---

## Inject 3 — The Decision Point (Containment)

**Time: Tuesday, 5:30 PM**

You've briefed the IR Lead. The situation:

- Marcus has systematically exfiltrated customer PII, pricing data, product roadmaps, and engineering files over 12 days
- He's leaving for a direct competitor
- He's still logged in and working normally
- His last day is Friday (3 days away)
- No one outside Security and IT knows about the investigation

**The IR Lead asks you:** "What's our containment plan? We need to act but we can't blow this."

**Complicating Factors:**
- It's 5:30 PM — HR Director Thomas Grant and General Counsel Sarah Whitfield are both offline (the playbook lists them as "business hours" availability)
- Marcus's manager doesn't know about the investigation
- If you disable Marcus's account now, he'll know immediately and could destroy evidence on personal devices
- If you wait until tomorrow, he could exfiltrate more data tonight

**Discussion Questions:**

1. Do you contain NOW or wait until HR/Legal are available tomorrow morning?
2. What specific containment actions from the playbook apply here? Are any missing?
3. How do you handle the "tipping off" risk vs. the "more exfiltration" risk?
4. Who has the authority to make this call? Is that clear in the playbook?
5. The playbook says to "coordinate with HR — security and HR action should happen simultaneously." HR is offline. What now?

---

## Inject 4 — Evidence & Legal (Preservation)

**Time: Wednesday, 8:00 AM**

HR and Legal are now engaged. Legal's first question: **"Will this evidence hold up if we pursue legal action for trade secret theft?"**

They need to know:

- Was all evidence collected with proper chain of custody?
- Were Marcus's communications monitored legally?
- Is the forensic image of his laptop admissible?
- Can you prove the data he took was actually classified as confidential?

**What you discover:**

- The DLP alert was logged, but nobody documented the initial triage steps or decision rationale
- The Salesforce exports were pulled from the audit log, but the log hasn't been exported and preserved — it's still just in the live system
- You don't have a forensic image of Marcus's laptop yet — you were waiting for Legal approval
- The company has no formal data classification policy — "confidential" isn't defined anywhere
- Marcus's NDA from 4 years ago mentions "proprietary information" but doesn't list specific data categories
- The SOC analyst reviewed Marcus's emails but didn't document authorization to do so

**Discussion Questions:**

1. What evidence handling steps did the playbook miss?
2. The playbook says "hash evidence at time of collection" — was that done?
3. Without a data classification policy, how do you prove the data was sensitive?
4. What should have happened differently with the laptop imaging?
5. Who authorizes employee email review? Is that in the playbook?

---

## Inject 5 — Escalation Chaos (Communication)

**Time: Wednesday, 11:00 AM**

Things are getting complicated:

- Marcus's manager overheard IT talking about "an investigation" and asked Marcus directly if everything was okay. Marcus is now visibly nervous and left for "lunch" at 10:45 AM — 15 minutes before the scheduled HR meeting.

- The CISO wants a briefing in 30 minutes and asks: "Is this a reportable data breach?" You realize:
  - 185,000 customer records with PII were exfiltrated
  - You operate in multiple states with different breach notification laws
  - You have customers in the EU (GDPR implications)
  - Nobody has assessed whether notification is required

- Marcus's competitor-employer posted a LinkedIn update this morning about "expanding their data analytics capabilities with exciting new talent."

**Discussion Questions:**

1. The playbook's communication plan failed — how? What was missing?
2. Who determines if this is a reportable breach? Is that role defined?
3. How do you handle Marcus potentially fleeing with evidence?
4. The playbook mentions "external communication per legal guidance only" — but Legal hasn't assessed notification obligations yet. What's the gap?
5. What do you do about the competitor's LinkedIn post?

---

## Inject 6 — Post-Incident (Lessons Learned)

**Time: Friday (end of week)**

The incident is contained. Here's where things landed:

- Marcus was terminated Wednesday afternoon when he returned from lunch. HR and Security coordinated the meeting.
- His laptop was imaged before the termination meeting (finally).
- Corporate counsel sent a cease-and-desist to Marcus and a preservation letter to the competitor.
- 185,000 customer records confirmed exfiltrated to personal Google Drive.
- An additional 340 engineering files and 14 confidential emails confirmed sent to personal accounts.
- Breach notification assessment is ongoing — Legal estimates 30+ days to determine state-by-state obligations.

**Final Review — What Broke:**

Think about every inject. Where did the playbook help, and where did it fail?

1. What worked well in the response?
2. What are the top 5 gaps you'd fix in the playbook?
3. What new procedures, tools, or policies would you recommend?
4. How would you restructure the contact roster to prevent the after-hours gap?
5. What detection improvements would catch this earlier than Day 12?
6. If you could redo this response from the beginning, what's the single biggest thing you'd change?

---

*End of exercise.*
