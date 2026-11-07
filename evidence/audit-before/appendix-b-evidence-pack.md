# Appendix B — Evidence Pack

**File:** `evidence/audit-before/appendix-b-evidence-pack.md`

**Repo:** AfterCastAI-Greenfield-Cloud-Migration-and-Compliance/

**Appendix:** B — Evidence Pack
**Version:** 1.0, 18th October 2026

---

## DISCLAIMER — READ FIRST — SIMULATED LAB EVIDENCE

**THIS APPENDIX CONTAINS SIMULATED EVIDENCE ONLY.**

All screenshots, headers, panels, and logs in this file are simulated mockups created for this AfterCast AI lab simulation. They do NOT come from a real Gmail account, a real Dropbox account, a real QNAP NAS, a real GitHub repo, a real Google Sheet, or a real USB drive. No real personal data was used. No real breach occurred. No real system was accessed.

AfterCast AI is fictional. LuxeMart UK is fictional. All personal Gmail addresses in this file, for example funmi.personal@gmail.com, ifeoma.personal@gmail.com, bola.personal@gmail.com, are fictional. They were created for this lab only. Do not attempt to send email to them. Any resemblance to a real person or a real account is coincidental.

**Why this pack exists:** In a real audit, Appendix B would contain real screenshots with real timestamps, real headers, and real redaction. In this lab, there is no real system to screenshot. This pack shows what an auditor would look for, what the real evidence would prove, and what the simulated mock proves for portfolio purposes. It is convincing as a demonstration of audit method, NOT as proof of a real breach.

**How to read each item:** Each item has: Evidence ID, Finding link, What real evidence would show, Simulated mock, Interpretation, Control failure, AFTER fix. The mock is labeled SIMULATED on every item. Do NOT present this pack to a regulator as real evidence.

**Related docs:** Audit Report 5, `docs/0.2-security-audit-report.md`, Sec 7, F1-01 to F2-07. Appendix A, Flow 1 BEFORE + Flow 2 BEFORE diagrams. Glossary, `docs/glossary.md`, Appendix C. RoPA, `compliance/RoPA.xlsx`.

**Appendix A update:** Flow 2 BEFORE diagram has been fixed. All labels now say Restricted-PII + Sensitive, not SPII. Transit C and Actor 2 now match Storage 1 and Storage 2. Appendix A is now consistent with Glossary and Audit Report 5.

---

## Evidence index

| Evidence ID | Finding | What it proves | Simulated mock | Status |
| :--- | :--- | :--- | :--- | :--- |
| E-B-01 | F2-06 | QNAP admin/admin, SMBv1, no encryption, no backup, no access log | QNAP admin panel mock | SIMULATED |
| E-B-02 | F1-03 | Dropbox anyone-with-link US, 12k EU, no DataClass enforcement | Dropbox sharing panel mock | SIMULATED |
| E-B-03 | F2-04 | Gmail BVN List email, Sent + Inbox, attachment unencrypted | Gmail header mock | SIMULATED |
| E-B-04 | F2-03 | Google Sheet open edit 30 staff, no audit log | Sheet sharing panel mock | SIMULATED |
| E-B-05 | F2-01 | Google Form, no privacy notice, no consent, NIN/BVN required | Form mock | SIMULATED |
| E-B-06 | F1-06 | GitHub public repo, model.pkl with raw emails | GitHub repo mock | SIMULATED |
| E-B-07 | F2-06 + F2-05 | USB in unlocked drawer + Finance Dell, no encryption, QuickBooks + CVs | USB + Dell inventory mock | SIMULATED |

**What is NOT in this pack:** No real screenshots, no real logs, no real email headers with Message-ID, no real CloudTrail logs, no real S3 Inventory. Those exist only in AFTER, in `/evidence/labs-after/`, with real Terraform + real AWS logs from Phase 1 to Phase 5. This pack covers BEFORE only, with simulated mocks.

---

## E-B-01 — QNAP NAS — F2-06

**Finding:** F2-06 — Flow 2 — Box 6 — QNAP NAS + USB — Critical
**Location:** Yaba Office Server Closet, QNAP NAS `//192.168.1.10/HR/CVs/`
**Owner:** Bola Tinubu (Finance) + Engineering, David Chen (CTO, Co-founder)

### What real evidence would show

In a real audit, the auditor would screenshot the QNAP admin panel, with:

- Hostname, IP, firmware version, uptime
- Shared folder `HR/CVs/`, permissions, guest access
- Admin user `admin`, password strength, last password change
- SMB version, encryption, access log
- Backup job, last backup, backup destination
- Access log, who accessed `HR/CVs/` in last 30 days

In BEFORE, the real panel would show: admin/admin, SMBv1, no encryption, no backup, no access log.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — QNAP QTS ADMIN PANEL — LAB MOCK — NOT REAL
AfterCast AI — Yaba Office Server Closet — QNAP NAS
================================================================
Hostname:      aftercast-qnap-01
IP:            192.168.1.10
Firmware:      QTS 5.1.2 (SIMULATED)
Uptime:        412 days (SIMULATED — never patched)

Shared Folder: HR/CVs/
Path:          //192.168.1.10/HR/CVs/
Permissions:   Everyone — Read / Write
Guest Access:  Enabled (SIMULATED)
Encryption:    NONE — Folder not encrypted
SMB Version:   SMBv1 — Enabled
  [X] Enable SMBv1 — Legacy, insecure, WannaCry vulnerable
  [ ] Enable SMBv2 / SMBv3
  [ ] Require encryption

Admin User:    admin
Password:      admin (SIMULATED — default, never changed)
Last Changed:  Never
MFA:           Not enabled
Last Login:    2026-10-12 09:14 (SIMULATED)

Backup Job:    NONE — No backup job configured
Last Backup:   Never
Backup Dest:   None

Access Log:    NONE — Access logging disabled
  No log for who accessed HR/CVs/ in last 30 days.

Files in HR/CVs/ (SIMULATED, redacted):
  - Applicant_001_CV.pdf — 245 KB — 2025-03-12
  - Applicant_002_CV.pdf — 189 KB — 2025-03-14
  - Hiring_2025_BVN_List.xlsx — 1.2 MB — 2025-06-18
    [Contains NIN, BVN, Passport — Restricted-PII + Sensitive]
  - ... 143 files total (SIMULATED)
    [Pre-Retention = 143; Post-Retention should be 11 hired
     + 0 rejected (132 deleted) — Actual = 143 retained forever]
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

QNAP is a single point of failure, with default credentials, legacy SMBv1, no encryption, no backup, no access log. Anyone on `192.168.1.0/24` can access `HR/CVs/` with admin/admin. Anyone who steals the QNAP or accesses the office can copy 143 applicants, including NIN + BVN + Passport, with no log.

### Control failure

GDPR Art 32, NDPA Sec 39 — No security. GDPR Art 5(1)(e), NDPA Sec 24(d) — No retention. NDPA Sec 44 — No DPIA.

### AFTER fix

Decommission QNAP + wipe, with proof. Migrate to S3 HR KMS `s3://aftercast-hr-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Block Public, Versioning, Lake Formation hr-group only, AWS Backup KMS + MFA Delete, Lifecycle Rejected 180d, Hired 6y. See Audit Report 5, Sec 6, Flow 2 AFTER.

---

## E-B-02 — Dropbox — F1-03

**Finding:** F1-03 — Flow 1 — Box 3 — Dropbox anyone-with-link US — Critical
**Location:** Personal Dropbox: funmi.personal.dropbox.com — Funmi Daniels (Sales, AE)
**Owner:** Funmi Daniels, Sales AE, Box 2 Flow 1

### What real evidence would show

In a real audit, the auditor would screenshot the Dropbox sharing panel, with:

- File name, size, owner, last modified
- Link settings: Anyone with link, Viewer, Editor, Expiry
- Who has access, who forwarded the link
- Location: US, no DataClass tag, no retention
- Version history, download log

In BEFORE, the real panel would show: Anyone with link can view, no expiry, no password, US, no DataClass tag.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — DROPBOX SHARING PANEL — LAB MOCK — NOT REAL
AfterCast AI — Flow 1 — LuxeMart UK — 12,000 EU
================================================================
File:          LuxeMart_Customer_List_2025.csv
Size:          4.8 MB (SIMULATED)
Owner:         Personal Dropbox: funmi.personal@gmail.com
               — Funmi Daniels (Sales, AE)
Uploaded:      2025-04-02 11:23 (SIMULATED)
Last Modified: 2025-04-02 11:23

Link:          https://www.dropbox.com/s/XXXXXXX/LuxeMart...
               [SIMULATED — Link redacted, not a real link]

Link Settings:
  [X] Anyone with link can view
  [ ] Specific people only
  [ ] Require password
  [ ] Set expiry date — NONE
  [ ] Disable downloads — NO

Access:
  - Anyone with link — Viewer
  - Link forwarded via WhatsApp Group AfterCast Sales
    [5 members + 2 ex-staff — See E-B-02 + Audit F1-04]
  - Download log: Not available in personal Dropbox

Location:      US (SIMULATED — Dropbox US)
DataClass:     NONE — No DataClass tag in BEFORE
  [Should be: Restricted-PII][DataSubject: EU][Count: 12k]

Content (SIMULATED, redacted, 2 rows of 12,000):
  Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History
  [REDACTED], [REDACTED]@example.co.uk, [REDACTED], [REDACTED], LM-001, ...
  [REDACTED], [REDACTED]@example.co.uk, [REDACTED], [REDACTED], LM-002, ...
  [All 12,000 rows in same file — No pseudonymisation]

Version History: 1 version — No DPA, no SCCs, no TIA
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

Dropbox link is forwardable, has no expiry, no password, no audit log. Anyone who gets the link via WhatsApp can download 12,000 EU customers, with Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History, with no log of who downloaded it.

### Control failure

GDPR Art 32, Art 44, NDPA Sec 39, Sec 41 — No security, no transfer safeguards. GDPR Art 28 — No DPA. GDPR Art 5(1)(f) — No confidentiality.

### AFTER fix

Prohibited. No Dropbox for PII. Use S3 Transfer Family SFTP mTLS + Lambda pseudonymisation + S3 KMS `s3://aftercast-prod-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Block Public, Versioning, Lake Formation sales-group only, DPA + SCCs + TIA. See Audit Report 5, Sec 6, Flow 1 AFTER.

---

## E-B-03 — Gmail BVN List — F2-04

**Finding:** F2-04 — Flow 2 — Box 4 — BVN List email — Critical
**Location:** Personal Gmail Sent + Inbox: ifeoma.personal@gmail.com Sent + bola.personal@gmail.com Inbox + Google US
**Owner:** Ifeoma Nnaji (P&C/HR) → Bola Tinubu (Finance)

### What real evidence would show

In a real audit, the auditor would screenshot the Gmail header, with:

- From, To, Subject, Date, Message-ID
- Attachment name, size, encryption
- Gmail Sent + Inbox copies, Gmail Trash, Google US location
- Forwarding, auto-forward, delegates

In BEFORE, the real header would show: Subject BVN List, attachment unencrypted, Sent + Inbox + Google US, Infinite.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — GMAIL HEADER — LAB MOCK — NOT REAL
AfterCast AI — Flow 2 — HR Applicants — 143 NG
================================================================
From:          Personal Gmail: ifeoma.personal@gmail.com
               — Ifeoma Nnaji (P&C/HR)
To:            Personal Gmail: bola.personal@gmail.com
               — Bola Tinubu (Finance)
Subject:       BVN List
Date:          2025-06-18 14:32 (SIMULATED)
Message-ID:    <SIMULATED — Redacted — NOT REAL>

Body (SIMULATED, redacted):
  Hi Bola,
  Attached is the sheet with BVN list for payroll.
  Please check and confirm.
  — Ifeoma

Attachment:    Hiring_2025_BVN_List.xlsx — 1.2 MB — Unencrypted
  [Contains: Name, Email, Phone, NIN, BVN, Passport — 143 rows]
  [DataClass: Restricted-PII + Sensitive][DataSubject: NG][Count: 143]
  [Pre-Retention = 143; Post-Retention should be 11 hired
   + 0 rejected (132 deleted)]

Copies:
  [X] Ifeoma Sent folder — Copy 1
  [X] Bola Inbox folder — Copy 2
  [X] Google US — Copy 3, Infinite
  [ ] WorkMail — NOT USED in BEFORE
  [ ] Pre-signed URL 1h — NOT USED in BEFORE

Location:      Google US (SIMULATED — Cross-border, no SCCs, no TIA)
Retention:     Infinite — Gmail Trash never emptied in BEFORE
Encryption:    NONE — Attachment unencrypted, no KMS, no password
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

Subject line itself is a breach. It tells anyone who sees the inbox that the email contains BVN List. Attachment is unencrypted, with 143 applicants, including NIN + BVN + Passport. Copies exist in Sent + Inbox + Google US, with no deletion. Anyone who compromises either Gmail account gets all 143.

### Control failure

GDPR Art 32, Art 44, NDPA Sec 39, Sec 41 — No security, no transfer safeguards. GDPR Art 5(1)(c) — Excessive. NDPA Sec 35 — No withdrawal.

### AFTER fix

Prohibited. No Gmail for PII. Use WorkMail `firstname.lastname@aftercast.ai` + pre-signed URL 1h, SSO + MFA, S3 HR KMS eu-west-1, Lake Formation hr-group only, no BVN List subject, no PII in subject line. See Audit Report 5, Sec 6, Flow 2 AFTER.

---

## E-B-04 — Google Sheet — F2-03

**Finding:** F2-03 — Flow 2 — Box 3 — Google Sheet open edit 30 staff US — Critical
**Location:** Google Sheet AfterCast Hiring 2025 - Responses
**Owner:** Ifeoma Nnaji (P&C/HR), Form on personal Google account

### What real evidence would show

In a real audit, the auditor would screenshot the Sheet sharing panel, with:

- Sheet name, owner, last modified
- Sharing: Anyone with link, Specific people, Editor, Viewer
- Who has access, 30 staff, ex-staff
- Location: US, no DataClass tag, no audit log, no version control for Sensitive

In BEFORE, the real panel would show: Open edit, 30 staff have access, Anyone can view responses, US, no audit log.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — GOOGLE SHEET SHARING PANEL — LAB MOCK — NOT REAL
AfterCast AI — Flow 2 — HR Applicants — 143 NG
================================================================
Sheet:         AfterCast Hiring 2025 - Responses
Owner:         Personal Gmail: ifeoma.personal@gmail.com
               — Ifeoma Nnaji (P&C/HR)
Created:       2025-03-01 (SIMULATED)
Last Modified: 2025-06-18 14:32 (SIMULATED)

Sharing:
  [X] Anyone with link — Editor
  [ ] Specific people only
  [ ] Viewer only

Who has access (SIMULATED, summary):
  - 30 staff — Editor — All 30 staff have access
  - Ifeoma Nnaji — Owner
  - Bola Tinubu — Editor
  - [REDACTED] — Ex-staff, still has access (SIMULATED)

Location:      Google US (SIMULATED — Cross-border, no SCCs, no TIA)
DataClass:     [Restricted-PII + Sensitive][DataSubject: NG][Count: 143]
  [Label added by auditor — No DataClass tag in BEFORE]

Content (SIMULATED, redacted, 2 rows of 143):
  Name | Email | Phone | NIN | BVN | Passport | CV Link
  [REDACTED] | [REDACTED] | [REDACTED] | [REDACTED] | [REDACTED] | [REDACTED] | ...
  [REDACTED] | [REDACTED] | [REDACTED] | [REDACTED] | [REDACTED] | [REDACTED] | ...

Form Settings:
  [X] Anyone can view responses
  [ ] Limit to 1 response
  [ ] Collect email addresses — YES, with NIN/BVN required

Audit Log:     NONE — No log for who viewed / edited / downloaded
Version History: 42 versions — No approval, no DPO review
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

Sheet is open edit to 30 staff, with no need-to-know, no audit log, no revocation for ex-staff. Anyone with link can edit, download, or forward 143 applicants, including NIN + BVN + Passport, with no log.

### Control failure

GDPR Art 32, Art 5(1)(f), NDPA Sec 39, Sec 24 — No security, no minimisation, no access control. GDPR Art 5(1)(c) — Excessive.

### AFTER fix

Prohibited. No Sheet for PII. Use S3 HR KMS `s3://aftercast-hr-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Lake Formation hr-group only, SSO + MFA, CloudTrail audit, Lifecycle Rejected 180d, Hired 6y. See Audit Report 5, Sec 6, Flow 2 AFTER.

---

## E-B-05 — Google Form — F2-01

**Finding:** F2-01 — Flow 2 — Box 1 — Google Form, no lawful basis for Sensitive — Critical
**Location:** Google Form, on personal Google account, Ifeoma Nnaji (P&C/HR)
**Owner:** Ifeoma Nnaji (P&C/HR)

### What real evidence would show

In a real audit, the auditor would screenshot the Google Form, with:

- Form title, questions, required fields
- Privacy notice, consent checkbox, lawful basis
- Who owns the Form, where responses go, who can view responses
- Retention, DSR, DPO contact

In BEFORE, the real Form would show: NIN, BVN, Passport required to apply, no privacy notice, no consent checkbox, no DPO contact, responses go to Sheet open edit 30 staff.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — GOOGLE FORM — LAB MOCK — NOT REAL
AfterCast AI — Flow 2 — HR Applicants — 143 NG
================================================================
Form Title:    AfterCast AI — Job Application Form
Owner:         Personal Gmail: ifeoma.personal@gmail.com
               — Ifeoma Nnaji (P&C/HR)
               [Form on personal Google account — NOT corporate]

Questions (SIMULATED, in order):
  1. Full Name * — Required
  2. Email * — Required
  3. Phone * — Required
  4. CV Upload * — Required
  5. NIN * — Required — [SIMULATED — Should NOT be required at application]
  6. BVN * — Required — [SIMULATED — Should NOT be required at application]
  7. Passport Number * — Required — [SIMULATED — Should NOT be required]
  8. Home Address — Required — [SIMULATED — Should NOT be required]

Privacy Notice:  NONE — No privacy notice at Form
  [Should be: GDPR Art 13, NDPA Sec 27 — Identity, purposes,
   lawful basis, recipients, retention, rights, DPO contact]

Consent:         NONE — No consent checkbox
  [Should be: Explicit consent checkbox for NIN / BVN,
   per NDPA Sec 35, GDPR Art 7, DPIA-HR-2025-01]

Lawful Basis:    NONE — No lawful basis documented
  [Should be: Contract + Legal Obligation + explicit consent
   for Sensitive + DPIA — See DPIA-HR-2025-01, Sec 4]

Where Responses Go:
  → Google Sheet AfterCast Hiring 2025 - Responses
    [Open edit 30 staff — See E-B-04]
  → Gmail notification to ifeoma.personal@gmail.com

Retention:       NONE — No retention stated at Form
  [Should be: Rejected 180 days, Hired 6 years — See Retention Policy]

DPO Contact:     NONE — No DPO contact at Form
  [Should be: Kenneth Pratt, Legal / Compliance]

Applicants:      143 total (SIMULATED)
  [11 hired + 132 rejected — Rejected 132 kept forever in BEFORE]
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

Form forces applicants to provide Sensitive data to apply, with no privacy notice, no consent, no lawful basis, no DPO contact, no retention. Applicants cannot freely refuse, because Form requires NIN/BVN to apply. Power imbalance is ignored.

### Control failure

GDPR Art 13, Art 5(1)(c), Art 35, NDPA Sec 27, Sec 35, Sec 44, Sec 24 — No transparency, no minimisation, no DPIA, no consent.

### AFTER fix

Prohibited. Use `careers.aftercast.ai`, API Gateway + Cognito + WAF, TLS 1.2, explicit consent checkbox, privacy notice at Form, CV only at application, NIN/BVN only after conditional offer, DPIA-HR-2025-01, DPO Kenneth Pratt. See Audit Report 5, Sec 6, Flow 2 AFTER.

---

## E-B-06 — GitHub Public Repo — F1-06

**Finding:** F1-06 — Flow 1 — Box 6 — GitHub public repo, model.pkl with raw emails — Critical
**Location:** GitHub public repo, Data Science, Amara Eze
**Owner:** Amara Eze, Data Science, Box 5 Flow 1

### What real evidence would show

In a real audit, the auditor would screenshot the GitHub repo, with:

- Repo name, visibility (Public), owner, last commit
- Files: `model.pkl`, `notebook.ipynb`, `requirements.txt`, etc.
- `model.pkl` size, contents, raw emails in model
- Clone count, fork count, stars, watchers
- Git history, who committed, when

In BEFORE, the real repo would show: Public, `model.pkl` with raw emails, Jupyter notebook with `SELECT *`, email as `user_id`, no DPA, no pseudonymisation.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — GITHUB PUBLIC REPO — LAB MOCK — NOT REAL
AfterCast AI — Flow 1 — LuxeMart UK — 12,000 EU
================================================================
Repo:          aftercast-forecasting-model (SIMULATED — Redacted)
Visibility:    [X] Public — Anyone can view, clone, fork
Owner:         [REDACTED] — Amara Eze (Data Science)
               [SIMULATED — Username redacted]
Last Commit:   2025-05-20 16:45 (SIMULATED)
Stars:         3 (SIMULATED) — Forks: 2 (SIMULATED)
Clones:        Unknown — Public repo, no audit log

Files (SIMULATED):
  - model.pkl — 48.2 MB — [Contains raw emails — See below]
  - notebook.ipynb — 2.1 MB — [Contains SELECT * + email as user_id]
  - requirements.txt — 1 KB
  - README.md — 2 KB — [No DPA, no DPIA, no privacy notice]

model.pkl — Contents (SIMULATED, redacted, NOT REAL):
  Model type:  RandomForest — Forecasting — What will they buy next?
  Training data: 12,000 rows — LuxeMart UK
  Features:    email as user_id, purchase_history, delivery_address
               [SIMULATED — Raw email used as user_id — Prohibited in AFTER]
  Memorisation: Model memorises emails — Model itself is PII
  Sample (SIMULATED, redacted):
    [REDACTED]@example.co.uk → Predicted: [REDACTED]
    [REDACTED]@example.co.uk → Predicted: [REDACTED]
  [All 12,000 emails in model — Deleting CSV does NOT delete model]

notebook.ipynb — Code (SIMULATED, redacted):
  import pandas as pd
  df = pd.read_sql("SELECT * FROM luxe_mart_customers", conn)
  # [SIMULATED — SELECT * — Prohibited in AFTER]
  # [SIMULATED — email as user_id — Prohibited in AFTER]
  model.fit(df[["email", "purchase_history"]], df["next_purchase"])
  # [SIMULATED — No pseudonymisation before training]

Git History:   12 commits — No DPO review, no model card, no DPIA
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

Public repo means anyone can clone `model.pkl` with 12,000 raw emails. Deleting the CSV does not delete the model. Model memorisation means the model itself is PII. Jupyter notebook shows `SELECT *` with email as `user_id`, with no pseudonymisation.

### Control failure

GDPR Art 32, Art 25, Art 5(1)(f), NDPA Sec 39, Sec 24 — No security, no pseudonymisation, no by design. GDPR Art 33 — Breach, if public repo confirmed.

### AFTER fix

Prohibited. No GitHub public for PII. Use Model Registry in AWS eu-west-1, private, hashed ID only, no model.pkl with raw emails, Model Card, memorisation test, bias test, DSR retraining log, `SELECT hashed_id, purchase_features` only, never `SELECT *`. See DPIA-LuxeMart-Profiling, Sec 9.

---

## E-B-07 — USB + Finance Dell — F2-06 + F2-05

**Finding:** F2-06 — Box 6 — QNAP + USB — Critical + F2-05 — Box 5 — Dell — High
**Location:** Yaba Office, Finance Dell + USB in unlocked drawer
**Owner:** Bola Tinubu (Finance), Box 5 Flow 2

### What real evidence would show

In a real audit, the auditor would photograph / screenshot:

- USB drive, label, location (unlocked drawer), encryption, contents
- Finance Dell, disk encryption, login, QuickBooks, CVs folder, email Inbox
- Office drawer, locked / unlocked, who has key
- QNAP + USB backup job, if any

In BEFORE, the real evidence would show: USB in unlocked drawer, no encryption, SanDisk 32GB PAYROLL BACKUP, Finance Dell no encryption, QuickBooks + CVs in same folder, Gmail Inbox with BVN List.

### Simulated mock — SIMULATED LAB EVIDENCE, NOT REAL

```
================================================================
SIMULATED — USB + FINANCE DELL INVENTORY — LAB MOCK — NOT REAL
AfterCast AI — Flow 2 — HR Applicants — 143 NG
================================================================
USB Drive:
  Label:         SanDisk 32GB — PAYROLL BACKUP (SIMULATED)
  Location:      Unlocked drawer — Yaba Office — Finance desk
                 [Drawer: Unlocked — Key in drawer — No lock]
  Encryption:    NONE — No BitLocker, no VeraCrypt, no password
  Contents (SIMULATED, redacted):
    - Hiring_2025_BVN_List.xlsx — 1.2 MB — [NIN, BVN, Passport — 143 rows]
    - CVs/ — 143 files — [CVs of 143 applicants]
    - Payroll_Backup_2025.xlsx — [Bank Details — 11 hired]
  Backup Job:    NONE — Manual copy, Dell → USB, no log
  Last Copy:     2025-06-18 (SIMULATED)

Finance Dell:
  Owner:         Bola Tinubu (Finance) — Finance Dell
  Location:      Yaba Office — Finance desk
  Disk Encryption: NONE — No BitLocker, no FileVault
  Login:         Password only — No MFA, no SSO
  Folders (SIMULATED):
    - C:\Users\Bola\Downloads\Hiring_2025_BVN_List.xlsx
      [Copy 5 — Finance Dell — See Flow 2 BEFORE]
    - C:\Users\Bola\Documents\QuickBooks\Payroll.qbw
      [QuickBooks + CVs in same laptop — No separation]
    - C:\Users\Bola\Downloads\CVs\ — 143 CVs
  Email:         Personal Gmail: bola.personal@gmail.com
                 — Bola Tinubu (Finance) — Inbox with BVN List
  QuickBooks:    QuickBooks Desktop — No MFA, no backup, no encryption

Office Drawer:
  Location:      Finance desk — Drawer 2 — Unlocked
  Key:           In drawer — Anyone in office can open
  Who has access: All staff in Yaba Office, plus visitors, plus cleaners
  [SIMULATED — No lock, no log, no CCTV in BEFORE]

Retention:       Infinite — USB never deleted, Dell never wiped
  [Should be: Rejected 180 days, Hired 6 years — See Retention Policy]
================================================================
END SIMULATED MOCK
================================================================
```

### Interpretation

USB in unlocked drawer means anyone in the office, plus visitors, plus cleaners, can take 143 applicants, including NIN + BVN + Passport, with no encryption, no log, no way to detect theft. Finance Dell has no encryption, no MFA, no separation between QuickBooks and CVs. Theft of Dell or USB = theft of all 143.

### Control failure

GDPR Art 32, NDPA Sec 39 — No security, no encryption, no access control. GDPR Art 5(1)(e), NDPA Sec 24(d) — No retention.

### AFTER fix

Prohibited. No USB for PII. No Dell local BVN. Decommission QNAP + wipe USB, with proof. Use S3 HR KMS `s3://aftercast-hr-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, AWS Backup KMS + MFA Delete, Lifecycle Rejected 180d, Hired 6y, Corp laptop BitLocker, SSO + MFA, WorkMail + pre-signed URL 1h, no local BVN. See Audit Report 5, Sec 6, Flow 2 AFTER.

---

## What a real Appendix B would also contain

In a real audit, Appendix B would also contain, with real timestamps and redaction:

- QNAP access log, or proof that no log exists
- Dropbox download log, or proof that no log exists
- Gmail Message-ID, SPF / DKIM / DMARC, forwarding rules
- Sheet version history, with who edited what, when
- Form response timestamps, with IP, with consent record
- GitHub clone log, with who cloned, when
- USB purchase receipt, Dell asset tag, BitLocker status screenshot
- Office CCTV, drawer lock, key log, if available
- DPO notes, interview notes with Funmi Daniels, Ifeoma Nnaji, Bola Tinubu, Amara Eze, with date, with signature

In this lab, those real items do not exist. This simulated pack is a stand-in for them, to show method, not to prove a real breach.

---

## How to use this pack in a portfolio

- Do NOT present this pack as real evidence. Present it as simulated lab evidence, with disclaimer on every page.
- Do present it as proof that you know what evidence to collect, what a real auditor would look for, and how to link evidence to findings.
- In an interview, say: Appendix B is simulated, because AfterCast AI is fictional. In a real engagement, I would collect the 7 items above, with real timestamps, real redaction, and real chain of custody, and I would link each to F1-01 to F2-07.
- In Phase 1 to Phase 5, collect real evidence in `/evidence/labs-after/`, with real Terraform, real AWS logs, real S3 Inventory, real CloudTrail, real Prowler, and add it to Appendix G + H. That evidence is real, because you built the AWS Landing Zone yourself.

---

## Sign-off

| Role | Name | Date | Signature |
| :--- | :--- | :--- | :--- |
| Owner, Cloud Security Engineer | Jeffrey Obi | 18th October 2026 |  |
| DPO | Kenneth Pratt, Legal / Compliance | 18th October 2026 |  |
| Approver, CEO | Aisha Bello, CEO | 18th October 2026 |  |

**Next review:** 18th April 2027, or earlier if processing changes, breach occurs, or new system is introduced.

**Appendix A update confirmed:** Flow 2 BEFORE diagram now uses Restricted-PII + Sensitive in all labels. Appendix A is consistent with Glossary and Audit Report 5.

