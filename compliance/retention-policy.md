# Retention Policy

**File:** `compliance/retention-policy.md`
**Repo:** AfterCastAI-Greenfield-Cloud-Migration-and-Compliance/

**Companion:** `compliance/data-classification-policy.md` — DataClass tags, Handling Rules, Terraform Enforcement. This policy defines how long each tag lives. Read them together. Do NOT duplicate DataClass definitions — link, do not copy.

**Status:** Approved for AFTER. BEFORE had no retention enforcement.

---

## Disclaimer: Lab Simulation and Fictional Addresses

All personal Gmail addresses in this file, for example funmi.personal@gmail.com, ifeoma.personal@gmail.com, bola.personal@gmail.com, are fictional. They were created for this AfterCast AI lab simulation only. They do not belong to real persons and do not correspond to real Gmail accounts. Do not attempt to send email to them. Any resemblance to a real person or a real account is coincidental. AfterCast AI is fictional. LuxeMart UK is fictional. All customer, applicant, and employee data in this file is simulated. No real personal data was used.

---

## 1. Document control

| Item | Fact |
| :--- | :--- |
| Policy ID | POL-RETENTION-2025-01 |
| Title | Retention Policy |
| Version | 1.0, 18th October 2026 |
| Owner | Kenneth Pratt, DPO, Legal / Compliance. Reports to Aisha Bello, CEO, not Engineering. GDPR Art 37, NDPA Sec 32. |
| Approver | Aisha Bello, CEO |
| Applies to | All staff, 30 staff, Yaba Lagos + London remote, all contractors, all systems, all data, BEFORE and AFTER |
| Related docs | Audit Report 5, `docs/0.2-security-audit-report.md` • RoPA, `compliance/RoPA.xlsx` • DPIA-HR-2025-01 • DPIA-LuxeMart-Profiling • Data Classification Policy, `compliance/data-classification-policy.md` • Appendix F, `compliance/compliance-mapping.md` |
| Review date | 18th April 2027, or earlier if new data type, new flow, breach, legal hold, or new regulation |
| Enforcement | Terraform + S3 Lifecycle + AWS Backup + CloudTrail + S3 Inventory + Macie. See Sec 7. |

**Purpose:** Data must NOT be kept longer than necessary. BEFORE kept everything forever, in 13 copies, with no deletion. AFTER deletes automatically, on time, with proof. If data is kept past its RetentionDays, that is a breach of GDPR Art 5(1)(e) and NDPA Sec 24(d), even if no one accessed it.

---

## 2. Principles

1. **Storage Limitation:** GDPR Art 5(1)(e), NDPA Sec 24(d). Keep personal data no longer than necessary for the purpose for which it was collected.
2. **1 Fact, 1 Name:** RetentionDays must match exactly across Audit, RoPA, DPIAs, Classification Policy, Terraform, and this policy. Do NOT invent new RetentionDays.
3. **RetentionDays are numbers, not techniques:** RetentionDays = 90, 180, 365, 1825. Pseudonymisation is a technique, NOT a RetentionDays value. Do NOT write RetentionDays = Pseudonymised.
4. **Logs are NOT PII:** Logs + deletion proof are kept 6 years for audit. PII is NOT kept 6 years. If a log contains PII, redact it before it is kept 6 years. Customer PII is deleted after 365 days (Flow 1) / 180 days rejected (Flow 2) / 6 years hired (Flow 2). Logs are kept 6 years. PII is NOT kept 6 years.
5. **Automatic deletion:** Retention is enforced by S3 Lifecycle + AWS Backup, not by manual deletion. Manual deletion is a backup, not the control.
6. **Proof, not trust:** Every deletion must have proof: S3 Inventory + CloudTrail DeleteObject + Macie + Lifecycle log, kept 6 years in Glacier Deep Archive, with hashed ID + DSR ID only, not raw PII.
7. **No copy survives retention:** Deletion must delete all copies: S3, Aurora, Model, Mapping, Backup, Logs, Trash, GitHub history, WhatsApp backup, Dropbox Trash. Deleting the CSV but keeping the model is NOT deletion. See Sec 6.

---

## 3. RetentionDays — Canonical values

**Only these 4 values exist for personal data. Do NOT invent 30, 60, 90 for PII unless DPO approves and updates this policy.**

| RetentionDays | What it means | Used for |
| :--- | :--- | :--- |
| 90 | 90 days | Internal logs, temporary files, pre-signed URL logs, WAF logs, if no PII, or pseudonymised only. Transition to Glacier Deep Archive after 90 days if needed for audit. |
| 180 | 180 days, 6 months | Flow 2 Rejected Applicants, 132 rejected. NDPA says rejected candidate data kept no longer than 6 months. |
| 365 | 365 days, 1 year | Flow 1 LuxeMart raw CSV, 12,000 EU. AfterCast retention for forecasting, as per DPA. |
| 1825 | 1825 days, 6 years | Flow 2 Hired Employees, 11 hired, employment / tax. Logs + deletion proof, 6 years, for audit, with PII NOT kept 6 years, except hired 11 who are kept 6 years by law. |

**How to tag:**

- S3 Tag: `RetentionDays = 365`, `RetentionDays = 180`, `RetentionDays = 1825`, etc.
- S3 Tag: `DataClass = Restricted-PII` or `Restricted-PII + Sensitive`, etc.
- Both tags are required. Bucket Policy denies PutObject if either tag is missing. See Classification Policy Sec 8.

**Examples:**

| Data | DataClass | RetentionDays | Residency |
| :--- | :--- | :--- | :--- |
| Flow 1 raw CSV, LuxeMart 12,000 EU | Restricted-PII | 365 | eu-west-1, eu-west-2 backup/DR |
| Flow 1 logs + deletion proof, hashed ID only | Internal | 1825, 6 years, proof only, PII NOT kept | eu-west-1, eu-west-2 backup/DR |
| Flow 1 model, hashed ID only | Restricted-PII | 365, retrained on DSR, model version deleted on DSR | eu-west-1 |
| Flow 2 rejected applicant, 132 | Restricted-PII + Sensitive | 180 | eu-west-1, eu-west-2 backup/DR |
| Flow 2 hired employee, 11 | Restricted-PII + Sensitive | 1825, 6 years | eu-west-1, eu-west-2 backup/DR |
| Flow 2 logs + deletion proof, hashed ID only | Internal | 1825, 6 years, proof only | eu-west-1, eu-west-2 backup/DR |

---

## 4. Retention by Flow — Full table

### 4.1 Flow 1 — LuxeMart UK, 12,000 EU, Restricted-PII

| Stage | Location | Retention | Action at expiry | Proof |
| :--- | :--- | :--- | :--- | :--- |
| Intake, Raw CSV | S3: `s3://aftercast-prod-restricted-pii-eu-west-1/raw/` | 365 days from intake date | S3 Lifecycle DeleteObject after 365 days | S3 Inventory + CloudTrail DeleteObject + Macie, kept 6y, hashed ID only |
| Pseudonymised features | Aurora: `aftercast-prod-aurora-eu-west-1`, table `forecasting_features`, `hashed_id, purchase_features` only | 365 days from intake date, same as raw, or earlier on DSR | Delete Aurora row for `hashed_id` + retrain model | Aurora deletion log + Model retraining log, kept 6y |
| Mapping table | S3: `s3://aftercast-prod-restricted-pii-eu-west-1/mapping/`, email → hashed_id, DPO + sales-group only | 365 days, same as raw, or earlier on DSR | Delete mapping row for `hashed_id` | S3 Inventory + CloudTrail, kept 6y, hashed ID only |
| Model | Model Registry, hashed ID only, versioned | 365 days, or earlier on DSR, model version tainted on DSR | Mark model version tainted, do NOT use for new forecasts, retrain without deleted hashed IDs, create new model version | Model Registry version log + retraining log, kept 6y |
| Logs | CloudTrail, S3 Inventory, Macie, GuardDuty, in eu-west-1 | 6 years, 1825 days, in Glacier Deep Archive, PII NOT kept | After 6 years, delete logs, with proof | Log retention log, kept 6y, then deleted |
| Deletion proof | Glacier Deep Archive, in eu-west-1 | 6 years, 1825 days | After 6 years, delete proof, with proof | Proof of proof, kept 6y, then deleted |

**Post-Retention, Flow 1:** After 365 days, 0 raw PII kept. 0 mapping rows kept. 0 Aurora rows kept, except logs proof with hashed ID only. Model retrained without deleted IDs.

**Clarification:** Customer PII deleted after 365 days. Logs + deletion proof kept 6 years for audit. PII is NOT kept 6 years.

### 4.2 Flow 2 — HR Applicants, 143 NG, Restricted-PII + Sensitive

| Stage | Location | Retention | Action at expiry | Proof |
| :--- | :--- | :--- | :--- | :--- |
| Application, CV only | S3 HR: `s3://aftercast-hr-restricted-pii-eu-west-1/`, via `careers.aftercast.ai` | Rejected: 180 days. Hired: 6 years. | Lifecycle auto-delete or auto-archive | S3 Inventory + CloudTrail, kept 6y |
| After offer, NIN, BVN, Passport, Bank Details | S3 HR, same bucket, KMS, Lake Formation hr-group only | Rejected: 180 days. Hired: 6 years. | Lifecycle auto-delete or auto-archive | S3 Inventory + CloudTrail, kept 6y |
| Rejected, 132 | S3 HR + Aurora HR | 180 days: Glacier 30 days → Delete 180 days | S3 Lifecycle Transition to Glacier after 30 days, Delete after 180 days. Delete Aurora row. | S3 Inventory + CloudTrail DeleteObject + Macie, kept 6y, hashed ID only |
| Hired, 11 | S3 HR + Aurora HR | 6 years, 1825 days: Transition to Glacier after 1 year, Transition to Deep Archive after 6 years, Delete after 6 years + audit proof | S3 Lifecycle Transition + Delete after 1825 days. Delete Aurora row after 1825 days. | S3 Inventory + CloudTrail, kept 6y |
| Logs | CloudTrail, S3 Inventory, Macie, GuardDuty, in eu-west-1 | 6 years, 1825 days, in Glacier Deep Archive, PII NOT kept beyond retention | After 6 years, delete logs, with proof | Log retention log, kept 6y, then deleted |
| Deletion proof | Glacier Deep Archive, in eu-west-1 | 6 years, 1825 days | After 6 years, delete proof, with proof | Proof of proof, kept 6y, then deleted |

**Post-Retention, Flow 2:** `[Pre-Retention = 143; Post-Retention = 11 hired + 0 rejected (132 deleted)]`

**BEFORE counts, Flow 2:** `[Pre-Retention = 143; Actual = 143 retained forever (132 should have been deleted after 180 days)]`

**NEVER write 143+132 = 275.**

### 4.3 DSR + Audit Logs, ROPA-04, Internal

| Stage | Location | Retention | Action at expiry | Proof |
| :--- | :--- | :--- | :--- | :--- |
| DSR log | S3 logs, CloudTrail, in eu-west-1 | 6 years, 1825 days, hashed ID + DSR ID only, no raw PII | Delete after 6 years | Log retention log |
| Audit log | CloudTrail, S3 Inventory, Macie, GuardDuty, in eu-west-1 | 6 years, 1825 days, hashed ID only if any, no raw PII | Delete after 6 years | Log retention log |
| Deletion proof | Glacier Deep Archive, in eu-west-1 | 6 years, 1825 days, hashed ID + DSR ID only | Delete after 6 years, with proof | Proof of proof |

---

## 5. Lifecycle — How deletion happens

### 5.1 S3 Lifecycle, Flow 1

- Day 0: Intake, S3 PutObject, Tags: `DataClass = Restricted-PII`, `RetentionDays = 365`, `Flow = Flow-1`
- Day 0 to 365: S3 Standard, KMS, Versioning, Lake Formation sales-group only
- Day 365: S3 Lifecycle DeleteObject, Versioned Delete Marker, S3 Inventory + CloudTrail DeleteObject + Macie proof to Glacier Deep Archive
- Day 365 to 1825: Proof in Glacier Deep Archive, hashed ID only, PII NOT kept
- Day 1825: Delete proof, with proof of proof

### 5.2 S3 Lifecycle, Flow 2 Rejected

- Day 0: Application, CV only, Tags: `DataClass = Restricted-PII + Sensitive`, `RetentionDays = 180`, `Flow = Flow-2`, `Status = Applicant`
- Day 0 to 30: S3 Standard, KMS, Lake Formation hr-group only
- Day 30: S3 Lifecycle Transition to Glacier, if rejected and 30 days passed
- Day 180: S3 Lifecycle DeleteObject, S3 Inventory + CloudTrail + Macie proof to Glacier Deep Archive
- Day 180 to 1825: Proof in Glacier Deep Archive, hashed ID only
- Day 1825: Delete proof, with proof of proof

### 5.3 S3 Lifecycle, Flow 2 Hired

- Day 0: Application, CV only, Tags: `DataClass = Restricted-PII + Sensitive`, `RetentionDays = 1825`, `Flow = Flow-2`, `Status = Hired`
- Day 0 to 365: S3 Standard, KMS, Lake Formation hr-group only
- Day 365: S3 Lifecycle Transition to Glacier
- Day 1825: S3 Lifecycle Transition to Deep Archive, then Delete, with proof
- Day 1825 to 3650: Proof in Glacier Deep Archive, hashed ID only, then delete proof

### 5.4 Aurora + Model + Mapping

- Aurora: Delete row for `hashed_id` on S3 Lifecycle Delete, via Lambda, with Aurora deletion log. No orphan rows.
- Mapping: Delete mapping row for `hashed_id` on S3 Lifecycle Delete, via Lambda, with S3 Inventory proof. No orphan mapping.
- Model: On DSR or on Retention expiry, mark model version tainted, retrain without deleted hashed IDs, create new model version, with retraining log. Deleting CSV but keeping model is NOT deletion.

### 5.5 What is NOT deletion

- Moving to Trash is NOT deletion. Dropbox Trash, Gmail Trash, WhatsApp backup, GitHub history, QNAP Old, USB, all must be emptied, with proof.
- Deleting the CSV but keeping the model is NOT deletion.
- Deleting the Sheet but keeping Gmail BVN List is NOT deletion.
- Deleting Gmail Inbox but keeping Gmail Sent is NOT deletion.
- Deleting QNAP but keeping USB is NOT deletion.
- All copies must be deleted, with proof, in all stores: S3, Aurora, Model, Mapping, Backup, Logs, Trash, GitHub history.

---

## 6. Deletion proof — What counts as proof

| Proof item | Where it lives | What it contains | What it must NOT contain |
| :--- | :--- | :--- | :--- |
| S3 Inventory | S3 Inventory, in eu-west-1, KMS, Object Lock | Object key, size, version, deletion date, `hashed_id`, DSR ID if DSR | Raw email, raw NIN, raw BVN, raw Passport, raw Bank Details |
| CloudTrail DeleteObject | CloudTrail, in eu-west-1, KMS, Object Lock | Event time, user, object key, deletion, `hashed_id`, DSR ID | Raw PII |
| Macie | Macie, in eu-west-1 | Scan result, no PII found after deletion, or PII found and remediated | Raw PII in Macie finding, must be redacted |
| Model retraining log | Model Registry, in eu-west-1 | Model version, training date, feature list, hashed ID count, DSR ID, retraining date | Raw email, raw PII |
| DSR log | S3 logs, in eu-west-1 | DSR ID, request type, date, status, `hashed_id`, deletion date | Raw PII |

**Retention of proof:** Proof kept 6 years, 1825 days, in Glacier Deep Archive, in eu-west-1, KMS, Object Lock, with PII NOT kept. After 6 years, proof deleted, with proof of proof.

**Who can see proof:** DPO Kenneth Pratt, Engineering, HR for Flow 2, Sales for Flow 1, as needed, via SSO + MFA, CloudTrail audit. No personal Gmail, no Dropbox, no WhatsApp.

---

## 7. Terraform enforcement — How retention is enforced, not just policy

Policy alone does not delete. Terraform + S3 Lifecycle + AWS Backup enforce. The following must be in Phase 1 + Phase 2 Terraform. Do NOT deploy without them.

### 7.1 S3 Lifecycle

- Flow 1: `s3://aftercast-prod-restricted-pii-eu-west-1/` — Lifecycle: Delete raw CSV after 365 days, Transition logs to Glacier Deep Archive after 90 days, Keep logs 6 years, PII NOT kept 6 years.
- Flow 2: `s3://aftercast-hr-restricted-pii-eu-west-1/` — Lifecycle: Rejected 132, Transition to Glacier after 30 days, Delete after 180 days. Hired 11, Transition to Glacier after 1 year, Transition to Deep Archive after 6 years, Delete after 1825 days.
- Both buckets: Versioning ON, KMS CMK, Object Lock ON for audit logs, Block Public Access ON, VPC Endpoint only, TLS 1.2 only.

### 7.2 AWS Backup

- Flow 1: AWS Backup, KMS, eu-west-1, Retention 365 days, same as S3, Delete backup after 365 days, with proof. No backup kept beyond retention.
- Flow 2: AWS Backup, KMS, eu-west-1, Retention 180 days for rejected, 1825 days for hired, MFA Delete ON, Versioning ON, Delete backup after retention, with proof. No backup kept beyond retention.
- All: Backup vault in eu-west-1, eu-west-2 backup/DR only, KMS CMK, Object Lock ON, CloudTrail audit.

### 7.3 Bucket Policies + SCPs

- Bucket Policy denies PutObject if `DataClass` or `RetentionDays` tag is missing. See Classification Policy Sec 8.
- Bucket Policy denies PutObject if `RetentionDays` != `365` for Flow 1, or not in `180, 1825` for Flow 2.
- SCP denies any action in any region except `eu-west-1` and `eu-west-2` for PII. Denies `us-*` for PII.
- SCP denies `s3:DeleteBucket` without MFA Delete for HR + Prod buckets.

### 7.4 What to do if Lifecycle does NOT delete

If S3 Lifecycle does NOT delete on time, do NOT ignore it. Tell Engineering + DPO within 24h. Manual deletion via DSR role, with proof, is backup. The missed Lifecycle is a finding. Log it in RoPA Change Log.

---

## 8. Legal hold — When retention is paused

Legal hold pauses deletion. Only DPO + CEO can place a legal hold. Legal hold must be in writing, with:

- Reason: Litigation, investigation, NDPC / ICO request, etc.
- Scope: Which `hashed_id`, which Flow, which date range
- Duration: Start date, end date, or until DPO lifts hold
- Who is notified: Engineering, HR, Sales, as needed

**Rules for legal hold:**

- Legal hold overrides Lifecycle. S3 Lifecycle must be paused for held objects, via Object Lock Legal Hold.
- Legal hold does NOT allow new collection. It only pauses deletion of existing data.
- Legal hold must be reviewed monthly by DPO. If hold is no longer needed, DPO lifts it, and Lifecycle resumes.
- Legal hold must be logged in RoPA Change Log, with DPO + CEO signatures.
- No legal hold exists in BEFORE. No legal hold is in place for Flow 1 + Flow 2 as of 18th Oct 2026, unless DPO places one in writing after this policy.

---

## 9. Exceptions — When retention can be extended

Exceptions are rare. Only DPO + CEO can approve an exception, in writing, with:

- Reason: Legal Obligation, e.g., tax law requires 6 years for hired, not 180 days for rejected.
- Scope: Which data, which Flow, which RetentionDays, new RetentionDays
- Duration: Start date, end date
- Risk: What is the risk of keeping longer, and how is it mitigated?

**What is NOT an exception:**

- We might need it later is NOT an exception.
- We forgot to delete it is NOT an exception, it is a breach.
- The model needs more data is NOT an exception, unless LuxeMart DPA + DPIA + DPO approve it, and pseudonymisation + minimisation are proven.
- The Sheet is still open is NOT an exception, it is a breach.

**No exceptions exist for Flow 1 + Flow 2 as of 18th Oct 2026, except hired 11 kept 6 years by law, which is already in this policy.**

---

## 10. BEFORE vs AFTER — What changed

| Area | BEFORE | AFTER |
| :--- | :--- | :--- |
| Retention | Infinite. Never deleted. Flow 1 365d violated, Flow 2 180d violated. 13 copies, 72,000 + 1,001 exposure points. | Flow 1 365d, Flow 2 rejected 180d, Flow 2 hired 6y, Lifecycle auto-delete, proof 6y, PII NOT kept 6y. |
| Deletion | No deletion. Dropbox Trash, Gmail Trash, WhatsApp backup, GitHub history, QNAP Old, USB, all Infinite. | Lifecycle auto-delete + S3 Inventory + CloudTrail + Macie proof. All copies deleted, with proof, in all stores. |
| Backup | No backup, QNAP no backup, no access log, single point of failure. Or backup kept forever, with no retention. | AWS Backup KMS + MFA Delete, Retention = S3 Retention, Delete backup after retention, with proof. |
| Logs | No logs, No CloudTrail, No Macie, No GuardDuty, No proof. | CloudTrail, S3 Inventory, Macie, GuardDuty, Object Lock, Glacier Deep Archive, proof 6y, hashed ID only. |
| Model | Model.pkl with raw emails, kept forever, GitHub public, no retraining on DSR. | Model Registry hashed ID only, retrained without deleted hashed IDs on DSR, model version tainted + retrained, with log. |
| Legal hold | No legal hold process. | Legal hold in writing, DPO + CEO only, monthly review, Object Lock Legal Hold, logged in RoPA. |

---

## 11. Roles

| Role | Who | What they do |
| :--- | :--- | :--- |
| DPO | Kenneth Pratt, Legal / Compliance | Owns this policy, approves RetentionDays, approves exceptions, approves legal holds, reviews quarterly, handles DSR + breach, reports to Aisha Bello, CEO, not Engineering. |
| CEO | Aisha Bello | Approves policy, approves exceptions, approves legal holds, approves residual risk. |
| Data Owner, Flow 1 | Funmi Daniels, Sales AE | Owns Flow 1 retention, ensures raw CSV deleted 365d, ensures model retrained on DSR, ensures DPA retention is 365d. |
| Data Owner, Flow 2 | Ifeoma Nnaji, P&C/HR | Owns Flow 2 retention, ensures rejected 180d, hired 6y, ensures Lifecycle is working, ensures deletion proof. |
| Data Owner, Finance | Bola Tinubu, Finance Manager | Owns payroll retention, ensures hired 6y, ensures no BVN List kept beyond retention. |
| Data Owner, DS | Amara Eze, Data Science | Owns model retention, ensures model retrained without deleted hashed IDs, ensures model card + retraining log. |
| Engineering / Cloud Security | David Chen, CTO, Co-founder + Emeka Nwosu, Engineering | Builds Terraform Lifecycle, AWS Backup, CloudTrail, S3 Inventory, Macie, GuardDuty, Object Lock. |
| All staff, 30 staff | All | Do NOT keep PII longer than RetentionDays. Do NOT use personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, GitHub for PII. Tell DPO if you find data kept past retention. |

---

## 12. Training and attestation

All staff must complete training on this policy before AFTER launch, and annually after that.

Training covers: RetentionDays 90, 180, 365, 1825, Storage Limitation, Lifecycle, Deletion Proof, What is NOT deletion, Legal Hold, Exceptions, How to report data kept past retention.

Attestation: Each staff member signs: I have read POL-RETENTION-2025-01, I understand RetentionDays, I will NOT keep PII longer than RetentionDays, I will NOT use personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, or GitHub public for PII, I will tell DPO if I find data kept past retention.

Training log: Kept by DPO, with date, attendance, attestation.

---

## 13. Appendix — Quick reference card

| Flow | Data | DataClass | RetentionDays | Action at expiry | Proof kept |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Flow 1, LuxeMart 12,000 EU | Raw CSV | Restricted-PII | 365 | Delete S3 + Aurora + Mapping + Model retrain | 6y, hashed ID only, PII NOT kept 6y |
| Flow 1, Logs | Logs + deletion proof | Internal | 1825, 6y | Delete logs after 6y | 6y, hashed ID only |
| Flow 2, Rejected 132 | CV + NIN, BVN, Passport | Restricted-PII + Sensitive | 180 | Glacier 30d → Delete 180d | 6y, hashed ID only |
| Flow 2, Hired 11 | CV + NIN, BVN, Passport, Bank | Restricted-PII + Sensitive | 1825, 6y | Glacier 1y → Deep Archive 6y → Delete | 6y, then delete proof |
| Flow 2, Logs | Logs + deletion proof | Internal | 1825, 6y | Delete logs after 6y | 6y, hashed ID only |

**Remember:** RetentionDays are numbers, not techniques. Pseudonymisation is a technique, NOT a RetentionDays value. Logs are kept 6y, PII is NOT kept 6y. Deleting the CSV but keeping the model is NOT deletion. All copies must be deleted, with proof.

**One fact, one file:** DataClass definitions live once in `compliance/data-classification-policy.md`. Do NOT copy DataClass tables here. Link to Classification Policy.

---

## 14. Sign-off

| Role | Name | Date | Signature |
| :--- | :--- | :--- | :--- |
| DPO | Kenneth Pratt, Legal / Compliance | 18th October 2026 |  |
| Owner, Flow 1 | Funmi Daniels, Sales AE | 18th October 2026 |  |
| Owner, Flow 2 | Ifeoma Nnaji, People + Culture | 18th October 2026 |  |
| Approver, CEO | Aisha Bello, CEO | 18th October 2026 |  |

**Next review:** 18th April 2027, or earlier if new data type, new flow, breach, legal hold, or new regulation.

