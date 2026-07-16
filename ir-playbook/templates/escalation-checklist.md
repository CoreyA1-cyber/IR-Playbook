# Escalation Checklist

Use this checklist to determine when and how to escalate an incident.

---

## Do I Need to Escalate?

Answer YES to any of these → escalate immediately:

- [ ] Severity is SEV-1 or SEV-2
- [ ] Scope has expanded beyond initial assessment
- [ ] Regulated data (PII, PHI, PCI, GDPR-covered) is confirmed involved
- [ ] An insider threat or employee is the subject of the investigation
- [ ] A containment action requires authorization beyond my level (see Authority Matrix)
- [ ] I need a decision that impacts business operations (system shutdown, customer notification)
- [ ] The incident may be a reportable data breach
- [ ] Law enforcement contact may be necessary
- [ ] I've been working the incident for 2+ hours with no resolution path
- [ ] Media or public attention is possible

---

## Who Do I Escalate To?

| Situation | Escalate To | Method |
|-----------|------------|--------|
| Any SEV-1 or SEV-2 | IR Lead → CISO | Phone + Slack |
| Employee/insider involved | IR Lead → HR Director → Legal | Phone |
| Regulated data at risk | IR Lead → Privacy Officer / DPO | Phone + Email |
| Potential breach notification | Privacy Officer → Legal | Phone |
| Business system shutdown needed | IR Lead → CISO | Phone (CISO authorizes) |
| Media/public inquiry about incident | CISO → Communications / PR | Phone |
| Law enforcement coordination | Legal → CISO | Phone |
| Primary contact unreachable (30 min) | Designated backup (see roster) | Cell phone |
| Backup also unreachable (15 min) | CISO (emergency authority) | Cell phone |

---

## After-Hours Escalation Path

```
Step 1: Call primary contact (cell phone)
        ↓ No answer in 30 minutes
Step 2: Call designated backup (cell phone)
        ↓ No answer in 15 minutes
Step 3: Call CISO (emergency authority for all SEV-1/2 decisions)
```

---

## What to Include in an Escalation

When escalating, provide:

1. **Incident ID** — the ticket number
2. **Current severity** — and why
3. **What happened** — 2-3 sentences
4. **What you've done so far** — actions taken
5. **What you need** — the specific decision or resource you're requesting
6. **Time sensitivity** — why this can't wait
