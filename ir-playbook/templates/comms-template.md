# Stakeholder Communication Templates

Use these templates to notify stakeholders during and after an incident. All external communications require Privacy Officer + Legal approval before sending.

---

## 1. Internal — Executive Briefing (SEV-1/2)

**Channel:** Email + phone briefing
**Audience:** CISO, CTO, CEO (as needed)
**Timing:** Within 30 minutes of SEV-1/2 classification

---

**Subject: [CONFIDENTIAL] Security Incident — SEV-[X] — [Brief Description]**

[CISO Name],

At [TIME] on [DATE], the SOC detected [brief description of the incident].

**Current Status:** [Active / Contained / Under Investigation]
**Severity:** SEV-[X]
**Affected Systems:** [List]
**Data at Risk:** [Type and estimated volume — e.g., "~185,000 customer records containing PII"]
**Business Impact:** [Current or potential impact to operations, customers, revenue]

**Actions Taken:**
- [Action 1]
- [Action 2]
- [Action 3]

**Next Steps:**
- [What's happening next and timeline]

**Decisions Needed:**
- [Any executive-level decisions required — e.g., customer notification, system shutdown]

I will provide the next update by [TIME]. Please contact me at [PHONE] for urgent questions.

[Your Name]
[Title]

---

## 2. Internal — HR Coordination (Insider Threat)

**Channel:** Private email or in-person — NOT Slack
**Audience:** HR Director (+ Legal)
**Timing:** As soon as insider threat is suspected with supporting evidence

---

**Subject: [CONFIDENTIAL — DO NOT FORWARD] Security Investigation — Employee Involved**

[HR Director Name],

The Security team has identified activity requiring HR coordination. This is a confidential investigation — please do not discuss with anyone outside the individuals listed below.

**Subject:** [Employee Name] — [Title] — [Department]
**Employment Status:** [Active / Notice Period / PIP]
**Summary:** [2-3 sentences describing the activity and evidence]
**Data Involved:** [Type and classification level]
**Recommended Action:** [What Security recommends — e.g., coordinated termination, administrative leave, access restriction]
**Timing Sensitivity:** [Why this needs to happen by X time]

**Need-to-Know List:** [Names of everyone currently aware of this investigation]

Requesting a meeting at your earliest availability to coordinate next steps. Please contact me by phone at [NUMBER] — do not reply to this email with investigation details.

[Your Name]

---

## 3. External — Customer Breach Notification

**Channel:** Formal written notice (email or letter)
**Audience:** Affected customers
**Timing:** Per Privacy Officer + Legal guidance — regulatory deadlines vary by jurisdiction
**APPROVAL REQUIRED:** Privacy Officer AND General Counsel must approve before sending

---

**Subject: Important Notice About Your Data**

Dear [Customer Name],

We are writing to inform you of a security incident that may have involved your personal information.

**What happened:** On [DATE], we identified unauthorized access to [SYSTEM] that contained [GENERAL DESCRIPTION — e.g., "customer contact information"]. We immediately launched an investigation and took steps to contain the incident.

**What information was involved:** The affected data may have included your [LIST SPECIFIC DATA TYPES — e.g., name, email address, phone number, billing address]. [IMPORTANT: Do not speculate beyond confirmed data types.]

**What we are doing:** Upon discovery, we [LIST ACTIONS — e.g., contained the unauthorized access, engaged external forensic investigators, notified law enforcement, enhanced our security controls].

**What you can do:** While we have no evidence that your information has been misused, we recommend:
- [Monitoring your accounts for unusual activity]
- [Being cautious of unsolicited communications requesting personal information]
- [Changing passwords for any accounts that shared credentials with our service]

[IF OFFERING CREDIT MONITORING: We are offering [X months] of complimentary credit monitoring through [PROVIDER]. Instructions to enroll are included below.]

**For more information:** If you have questions, please contact [PHONE / EMAIL / URL] between [HOURS].

We take the security of your information seriously and sincerely regret that this incident occurred.

Sincerely,
[Executive Name]
[Title]
[Company]

---

## 4. External — Regulatory Notification

**Channel:** Per regulatory body's specified method
**Audience:** Relevant supervisory authority (state AG, HHS for HIPAA, ICO/DPA for GDPR)
**APPROVAL REQUIRED:** Privacy Officer AND General Counsel draft and submit

> Note: This template is a starting framework only. Regulatory notifications have jurisdiction-specific requirements. The Privacy Officer and Legal must tailor the notification to each applicable regulation.

---

**Key Information to Include:**

1. Nature of the breach (what happened)
2. Categories and approximate number of individuals affected
3. Categories of personal data involved
4. Likely consequences of the breach
5. Measures taken or proposed to address the breach
6. Contact details for the DPO or Privacy Officer
7. Timeline of events (discovery, containment, notification)

**Regulatory Deadlines:**
- **GDPR (Article 33):** 72 hours from awareness to supervisory authority
- **US State Laws:** Vary by state (30–90 days typical; some require "without unreasonable delay")
- **HIPAA:** 60 days to HHS; 60 days to affected individuals
- **Contractual:** Check enterprise customer agreements for notification SLAs
