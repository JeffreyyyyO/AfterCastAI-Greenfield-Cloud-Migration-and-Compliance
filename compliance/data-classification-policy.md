# Data Classification Policy

**File:** `compliance/data-classification-policy.md`
**Repo:** AfterCastAI-Greenfield-Cloud-Migration-and-Compliance/

**Companion:** `compliance/retention-policy.md` — RetentionDays, Lifecycle, Deletion Proof. This policy defines DataClass tags. Retention Policy defines how long each tag lives. Read them together. Do NOT duplicate retention tables — link, do not copy.

**Status:** Approved for AFTER. BEFORE had no classification.

---

## Disclaimer: Lab Simulation and Fictional Addresses

All personal Gmail addresses in this file, for example funmi.personal@gmail.com, ifeoma.personal@gmail.com, bola.personal@gmail.com, are fictional. They were created for this AfterCast AI lab simulation only. They do not belong to real persons and do not correspond to real Gmail accounts. Do not attempt to send email to them. Any resemblance to a real person or a real account is coincidental. AfterCast AI is fictional. LuxeMart UK is fictional. All customer, applicant, and employee data in this file is simulated. No real personal data was used.

---

## 1. Document control

| Item | Fact |
| :--- | :--- |
| Policy ID | POL-DATA-CLASS-2025-01 |
| Title | Data Classification Policy |
| Version | 1.0, 18th October 2026 |
| Owner | Kenneth Pratt, DPO, Legal / Compliance. Reports to Aisha Bello, CEO, not Engineering. GDPR Art 37, NDPA Sec 32. |
| Approver | Aisha Bello, CEO |
| Applies to | All staff, 30 staff, Yaba Lagos + London remote, all contractors, all systems, all data, BEFORE and AFTER |
| Related docs | Audit Report 5, `docs/0.2-security-audit-report.md` • RoPA, `compliance/RoPA.xlsx` • DPIA-HR-2025-01 • DPIA-LuxeMart-Profiling • Appendix F, `compliance/compliance-mapping.md` • Retention Policy, `compliance/retention-policy.md` |
| Review date | 18th April 2027, or earlier if new data type, new flow, breach, or new regulation |
| Enforcement | Terraform + AWS SCPs + S3 Bucket Policies + Lake Formation + Manual review. See Sec 8. |

**Purpose:** Every piece of data at AfterCast AI must have one DataClass tag. No data lives without a tag. No data moves without a tag check. BEFORE failed because no data had a tag, so no control could be enforced. AFTER fixes it by tagging at intake, enforcing tags in Terraform, and blocking untagged data.

---

## 2. Principles

1. **1 Fact, 1 Name:** DataClass tag must match exactly across Audit, Diagrams, RoPA, DPIAs, Terraform, and this policy. Do NOT invent new tags.
2. **Default to higher class:** If you are unsure, classify higher, not lower. If data contains one Sensitive field, the whole file is Restricted-PII + Sensitive, not Restricted-PII.
3. **Tag at intake:** Classification happens when data is created or received, not later. Lambda classifies at S3 intake. Form classifies at submission. No manual download then classify.
4. **Least privilege:** Access is by DataClass + Group, not by person. Sales-group sees Restricted-PII for Flow 1. HR-group sees Restricted-PII + Sensitive for Flow 2. No one sees both unless DPO approves.
5. **Pseudonymisation is NOT declassification:** Pseudonymised data is still personal data. `sha256_hashed_id` is still Restricted-PII, until it is truly anonymised and re-identification is impossible. Do NOT downgrade pseudonymised data to Internal.
6. **Pseudonymisation is a technique, NOT a RetentionDays value:** RetentionDays is 90, 180, 365, 1825. Do NOT write RetentionDays = Pseudonymised.

---

## 3. DataClass tags — Canonical list

**Only these 5 tags exist. Do NOT use SPII. Do NOT use PII, Sensitive, Confidential-PII, or any other tag.**

| DataClass Tag | What it means | Examples at AfterCast AI |
| :--- | :--- | :--- |
| `Public` | Public marketing, no PII, can be published | Public website copy, public blog, public job advert with no applicant data |
| `Internal` | Internal docs, no PII, not for public | Internal runbooks, architecture diagrams with no PII, DSR logs with hashed ID only, meeting notes with no PII |
| `Confidential` | Business confidential, no PII, harm if leaked | Pricing, contracts, vendor terms, roadmap, source code with no secrets, internal financial forecasts with no PII |
| `Restricted-PII` | Customer PII, EU, identifiable, harm if leaked is phishing, fraud, spam | Flow 1: LuxeMart UK, 12,000 EU, Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History |
| `Restricted-PII + Sensitive` | Applicant / employee PII + Sensitive, NIN, BVN, Passport, Bank Details, harm if leaked is identity takeover, bank fraud, SIM swap | Flow 2: HR Applicants, 143 NG, CV, NIN, BVN, Passport scan, Home Address, Bank Details |

**How to choose:**

- Does it contain Name, Email, Phone, Address, Loyalty ID, Purchase History? → `Restricted-PII`
- Does it contain NIN, BVN, Passport, Bank Details, or any national ID / financial ID? → `Restricted-PII + Sensitive`, even if it also contains Name, Email, Phone. Sensitive wins.
- Does it contain both customer PII and Sensitive? → `Restricted-PII + Sensitive`
- Does it contain no PII, but business harm if leaked? → `Confidential`
- Does it contain no PII and no business harm? → `Internal`
- Is it already public? → `Public`

**Examples that are often misclassified:**

| Data | Wrong tag | Correct tag | Why |
| :--- | :--- | :--- | :--- |
| LuxeMart CSV with Name, Email, Purchase History | `Internal` or `Confidential` | `Restricted-PII` | Contains PII, must be Restricted-PII |
| HR Sheet with NIN, BVN | `Restricted-PII` | `Restricted-PII + Sensitive` | Contains Sensitive, must be + Sensitive |
| Model.pkl trained on raw emails | `Internal` or `Confidential` | `Restricted-PII` | Model memorises emails, model is PII |
| DSR log with hashed ID only, no raw email | `Restricted-PII` | `Internal` | Hashed ID only, no raw PII, if mapping is separate and access is controlled. If mapping is in same file, it is Restricted-PII. |
| S3 logs with raw email in log line | `Internal` | `Restricted-PII` | Log contains PII, must be redacted or re-tagged |
| Mapping table email → hashed_id | `Internal` | `Restricted-PII + Sensitive` if Flow 2, `Restricted-PII` if Flow 1 | Mapping re-identifies, must be highest class of data it maps |

---

## 4. Handling rules by DataClass

### 4.1 Public

| Rule | Fact |
| :--- | :--- |
| Storage | Any approved store, S3 Public bucket only if DPO approves, no KMS required |
| Encryption | TLS 1.2 in transit, no KMS required at rest |
| Access | Anyone, no SSO required |
| Sharing | Can be shared publicly, no DPA required |
| Residency | Any region, no restriction |
| Retention | Per Retention Policy, or until superseded |
| Disposal | Delete, no proof required |

### 4.2 Internal

| Rule | Fact |
| :--- | :--- |
| Storage | S3 Internal, Google Drive corporate only if approved, no personal Drive, no personal Gmail |
| Encryption | TLS 1.2 in transit, S3 SSE-S3 at rest, no KMS CMK required |
| Access | SSO + MFA, any staff, no group restriction |
| Sharing | Internal only, no external sharing without DPO approval, no anyone-with-link |
| Residency | eu-west-1 primary, eu-west-2 backup/DR only |
| Retention | Per Retention Policy. DSR logs + deletion proof: 6 years in Glacier Deep Archive, hashed ID only, PII NOT kept 6 years. |
| Disposal | Delete + CloudTrail proof, no PII proof required |

### 4.3 Confidential

| Rule | Fact |
| :--- | :--- |
| Storage | S3 Confidential, KMS CMK, eu-west-1, no personal Drive, no personal Gmail, no Dropbox, no WhatsApp |
| Encryption | TLS 1.2 in transit, KMS CMK at rest |
| Access | SSO + MFA, need-to-know group, DPO approves group |
| Sharing | Internal + named external with NDA, no anyone-with-link, pre-signed URL 1h max if needed |
| Residency | eu-west-1 primary, eu-west-2 backup/DR only |
| Retention | Per Retention Policy, 90 days to 365 days, to be confirmed per document type |
| Disposal | Delete + CloudTrail + S3 Inventory proof |

### 4.4 Restricted-PII — Flow 1, LuxeMart UK, 12,000 EU

| Rule | Fact |
| :--- | :--- |
| Storage | S3: `s3://aftercast-prod-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Block Public, Versioning, Object Lock for audit logs. Aurora: `aftercast-prod-aurora-eu-west-1`, KMS, hashed ID only. Model Registry: hashed ID only, no raw email, no model.pkl with raw emails. |
| Encryption | TLS 1.2 in transit, SFTP mTLS for intake, KMS CMK at rest, VPC Endpoint PrivateLink, no public internet |
| Access | SSO + MFA, Identity Center, Lake Formation sales-group only, DS group for training only, no Finance, no HR, no 30 staff, no ex-staff. Access logged in CloudTrail. |
| Sharing | Only on documented instructions from LuxeMart in DPA. No sharing with other clients. No sharing via Dropbox, WhatsApp, Gmail, GitHub, WeTransfer. Pre-signed URL 1h max, via WorkMail, only if DPA allows it. |
| Residency | eu-west-1 primary, eu-west-2 backup/DR only. No US regions for PII. No personal Gmail US, no Dropbox US, no WhatsApp US, no GitHub US. |
| Retention | Raw CSV: 365 days, then auto-delete. Logs + deletion proof: 6 years in Glacier Deep Archive, PII NOT kept 6 years. Model: retrained without deleted hashed IDs on DSR. See Retention Policy. |
| Disposal | Lifecycle auto-delete 365d + S3 Inventory + CloudTrail DeleteObject + Macie proof. Model version tainted + retrained on DSR. Proof kept 6y, with hashed ID + DSR ID only, not raw email. |
| Pseudonymisation | Email → `sha256_hashed_id` with salt, before training. Salt in Secrets Manager, KMS, not in code, not in model, not in Aurora training table. Mapping table separate, S3 KMS + Lake Formation, sales-group + DPO only, not DS. Training table: `hashed_id, purchase_features` only, no name, no email, no phone, no address. |
| Transfers | SFTP mTLS + DPA + SCCs + TIA. AWS DPA + SCCs, eu-west-1 only. No transfers to US, no Dropbox, no WhatsApp, no GitHub. |
| DPIA | DPIA-LuxeMart-Profiling, GDPR Art 35, NDPA Sec 44. DPA required before intake. Art 22 assessment required if forecast used for legal / similarly significant effect. |

### 4.5 Restricted-PII + Sensitive — Flow 2, HR Applicants, 143 NG

| Rule | Fact |
| :--- | :--- |
| Storage | S3: `s3://aftercast-hr-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Block Public, Versioning, Object Lock for audit logs. Aurora HR: `aftercast-prod-aurora-hr-eu-west-1`, KMS, AWS Backup KMS + MFA Delete. Careers: `careers.aftercast.ai`, API Gateway + Cognito + WAF. WorkMail: `firstname.lastname@aftercast.ai`, pre-signed URL 1h. No QNAP, No USB, No Sheet, No Gmail, No Dell local BVN. |
| Encryption | TLS 1.2 in transit, KMS CMK at rest, VPC Endpoint, no SMBv1, no public internet |
| Access | SSO + MFA, Identity Center, Lake Formation hr-group only, HR + Finance for payroll only, no 30 staff, no Sales, no DS, no ex-staff. Access logged in CloudTrail. |
| Sharing | Internal HR + Finance only. No external sharing without DPO + explicit consent. No sharing via Gmail, Sheet, Dropbox, WhatsApp, USB. Pre-signed URL 1h max, via WorkMail, only for HR + Finance. |
| Residency | eu-west-1 primary, eu-west-2 backup/DR only. No US regions for PII. No Google US, no Gmail US, no Sheet US. |
| Retention | Rejected 132: Glacier 30 days → Delete 180 days. Hired 11: 6 years → Deep Archive. Lifecycle auto-delete. Proof kept 6 years, PII NOT kept beyond retention. See Retention Policy. Post-Retention: 11 hired + 0 rejected (132 deleted). |
| Disposal | Lifecycle auto-delete 180d / 6y + S3 Inventory + CloudTrail DeleteObject + Macie proof. QNAP decommissioned + wiped, USB wiped, Sheet deleted, Gmail BVN List deleted, Gmail Trash emptied. |
| Collection | CV only at application. NIN, BVN, Passport, Bank Details, Home Address only after conditional offer, via `careers.aftercast.ai`, with explicit consent checkbox, privacy notice at Form. Minimisation filter at ingestion. |
| Transfers | careers.aftercast.ai + API Gateway + Cognito + WAF, TLS 1.2, eu-west-1 only. AWS DPA + SCCs if needed. No transfers to US, no Gmail, no Sheet, no QNAP, no USB. |
| DPIA | DPIA-HR-2025-01, GDPR Art 35, NDPA Sec 44. Privacy notice at Form, GDPR Art 13, NDPA Sec 27. Withdrawal via DSR API, NDPA Sec 35, GDPR Art 7. |

---

## 5. Labelling

### 5.1 How to label

| System | How to label |
| :--- | :--- |
| S3 | Tag: `DataClass = Restricted-PII` or `Restricted-PII + Sensitive`, etc. Tag: `Owner = Funmi Daniels` or `Ifeoma Nnaji`, etc. Tag: `Flow = Flow-1` or `Flow-2`, etc. Tag: `RetentionDays = 365` or `180` or `1825`, etc. Bucket Policy denies PutObject without DataClass tag. See Sec 8. |
| Aurora | Table comment: `DataClass = Restricted-PII`, etc. Column comment for Sensitive columns. No Sensitive column without DataClass tag. |
| Lambda | Environment variable: `DataClass = Restricted-PII`, etc. Log line includes DataClass, no PII in log. |
| Email / WorkMail | Subject prefix: `[Restricted-PII]` or `[Restricted-PII + Sensitive]`, etc. Body footer: DataClass + Retention + DPO contact. No PII in subject line. Never write `BVN List` in subject. |
| Documents / Sheets | Header: DataClass + Owner + Retention + Date. Footer: Do NOT share via personal Gmail, Dropbox, WhatsApp. |
| Model Registry | Model card: DataClass, Feature list, Training date, Hashed ID count, DSR retraining log, DPIA ref. |

### 5.2 What to do if you find untagged data

1. Stop. Do NOT forward, download, or share it.
2. Classify it using Sec 3. If unsure, classify higher.
3. Tag it, or move it to S3 with correct tag, via WorkMail + pre-signed URL, not via personal Gmail.
4. Tell DPO Kenneth Pratt. DPO logs it in RoPA Change Log.
5. If it is Restricted-PII or Restricted-PII + Sensitive in personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, or GitHub, treat it as a potential breach. Follow Breach Playbook, notify DPO within 24h internal.

---

## 6. Roles

| Role | Who | What they do |
| :--- | :--- | :--- |
| DPO | Kenneth Pratt, Legal / Compliance | Owns this policy, approves DataClass tags, approves sharing, reviews quarterly, handles DSR + breach, reports to Aisha Bello, CEO, not Engineering. GDPR Art 37, NDPA Sec 32. |
| CEO | Aisha Bello | Approves policy, approves residual risk, approves DPA + TIA. |
| Data Owner, Flow 1 | Funmi Daniels, Sales AE | Owns Flow 1 data, ensures LuxeMart intake is SFTP mTLS, ensures DPA + TIA are signed, ensures no Dropbox, no WhatsApp, no Gmail. |
| Data Owner, Flow 2 | Ifeoma Nnaji, P&C/HR | Owns Flow 2 data, ensures CV only at application, NIN/BVN only after offer, ensures no Sheet, no Gmail, no QNAP, no USB. |
| Data Owner, Finance | Bola Tinubu, Finance Manager | Owns payroll data, ensures no BVN List email, ensures pre-signed URL + WorkMail + SSO + MFA. |
| Data Owner, DS | Amara Eze, Data Science | Owns model, ensures pseudonymisation before training, ensures no raw email in model, ensures retraining on DSR, ensures model card. |
| Engineering / Cloud Security | David Chen, CTO, Co-founder + Emeka Nwosu, Engineering | Builds Terraform enforcement, S3 KMS, Lake Formation, VPC Endpoint, Lifecycle, CloudTrail, Macie, GuardDuty. |
| All staff, 30 staff | All | Tag data at creation, do NOT use personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, GitHub public for PII. Tell DPO if you find untagged data. |

---

## 7. BEFORE vs AFTER — What changed

| Area | BEFORE | AFTER |
| :--- | :--- | :--- |
| Tagging | No DataClass tag. Dropbox had no tag, Sheet had no tag, Gmail had no tag, QNAP had no tag, GitHub had no tag. | Every S3 object, Aurora table, Lambda, email, document, model has DataClass tag. Bucket Policy denies untagged PutObject. |
| Access | 30 staff had Sheet edit, 5 + 2 ex-staff had WhatsApp, anyone-with-link had Dropbox, public had GitHub. | Lake Formation sales-group only for Flow 1, hr-group only for Flow 2, SSO + MFA, CloudTrail audit, revoke on exit. |
| Storage | Personal Gmail, Personal Dropbox US, Personal Drive, WhatsApp, Google Sheet US, QNAP admin/admin, USB unlocked, Local Postgres, GitHub public. | S3 KMS eu-west-1, Aurora KMS eu-west-1, Model Registry hashed ID only, WorkMail + pre-signed URL 1h, no personal stores. |
| Transfers | WeTransfer no encryption, Dropbox US, WhatsApp US, Gmail US, GitHub US, no DPA, no SCCs, no TIA. | SFTP mTLS, S3 KMS eu-west-1, VPC Endpoint, DPA + SCCs + TIA, eu-west-2 backup/DR only, no US for PII. |
| Retention | Infinite. Never deleted. Flow 1 365d violated, Flow 2 180d violated. | Flow 1 365d, Flow 2 rejected 180d, Flow 2 hired 6y, Lifecycle auto-delete, proof 6y, PII NOT kept 6y. |
| Pseudonymisation | No pseudonymisation. SELECT * with email as user_id. Model.pkl with raw emails. | Email → sha256_hashed_id before training. Mapping separate. Training table hashed ID only. Model hashed ID only. Retraining on DSR. |

---

## 8. Terraform enforcement — How tags are enforced, not just policy

Policy alone does not enforce. Terraform enforces. The following must be in Phase 1 + Phase 2 Terraform. Do NOT deploy without them.

### 8.1 SCPs — AWS Organizations

- Deny `s3:PutObject` if `aws:RequestTag/DataClass` is missing.
- Deny `s3:PutObject` if `aws:RequestTag/RetentionDays` is missing.
- Deny any action in any region except `eu-west-1` and `eu-west-2` for PII. Deny `us-*` for PII.
- Deny `s3:PutBucketPublicAccessBlock` disable, Deny `s3:PutBucketAcl` public, Deny `s3:PutObjectAcl` public.
- Deny `iam:CreateUser` without MFA, Deny `s3:DeleteBucket` without MFA Delete for HR + Prod buckets.

### 8.2 S3 Bucket Policies

- `s3://aftercast-prod-restricted-pii-eu-west-1/` — Deny PutObject if `DataClass` != `Restricted-PII`. Deny PutObject if `RetentionDays` != `365`. Deny GetObject if `aws:PrincipalTag/Group` != `sales-group` and != `dpo-audit`. Deny DeleteObject except Lifecycle + DSR role.
- `s3://aftercast-hr-restricted-pii-eu-west-1/` — Deny PutObject if `DataClass` != `Restricted-PII + Sensitive`. Deny PutObject if `RetentionDays` not in `180, 1825`. Deny GetObject if `aws:PrincipalTag/Group` != `hr-group` and != `finance-group` and != `dpo-audit`. Deny DeleteObject except Lifecycle + DSR role.
- Both buckets: Block Public Access ON, Versioning ON, KMS CMK, Object Lock ON for audit logs, VPC Endpoint only, TLS 1.2 only.

### 8.3 Lake Formation

- Flow 1: Database `prod`, Table `forecasting_features`, Grant SELECT to `sales-group` only, Columns `hashed_id, purchase_features` only, No SELECT *.
- Flow 2: Database `hr`, Table `applicants`, Grant SELECT to `hr-group` only, Grant SELECT payroll columns to `finance-group` only, No SELECT * for Sensitive columns without DPO approval.
- Mapping table: Grant SELECT to `dpo-audit` only, not DS, not Sales, not HR.

### 8.4 Lifecycle + Retention

- Flow 1: S3 Lifecycle Delete raw CSV after 365 days, Transition logs to Glacier Deep Archive after 90 days, Keep logs 6 years, PII NOT kept 6 years.
- Flow 2 Rejected: S3 Lifecycle Transition to Glacier after 30 days, Delete after 180 days.
- Flow 2 Hired: S3 Lifecycle Transition to Glacier after 1 year, Transition to Deep Archive after 6 years, Delete after 6 years + audit proof.
- All: S3 Inventory + CloudTrail + Macie proof, kept 6 years in Glacier Deep Archive, hashed ID + DSR ID only, not raw PII.

### 8.5 What to do if Terraform blocks you

If Terraform or AWS blocks a PutObject, GetObject, or DeleteObject, do NOT work around it with personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, or GitHub. Tell Engineering + DPO. The block is the control working. Workaround is a breach.

---

## 9. Breach, DSR, and audit

| Topic | Rule |
| :--- | :--- |
| Breach | If Restricted-PII or Restricted-PII + Sensitive is in personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, GitHub, or any untagged store, notify DPO within 24h internal. DPO assesses 72h notification to NDPC / ICO, GDPR Art 33, NDPA Sec 40. For Flow 1, notify LuxeMart within 24h internal, to allow LuxeMart 72h to ICO. |
| DSR | Access, Erasure, Portability, Objection, Withdrawal via DSR API POST /dsr, with hashed ID. See DPIA-HR-2025-01 Sec 9 and DPIA-LuxeMart-Profiling Sec 9. For Flow 1, DSR via LuxeMart, with hashed ID + retraining. For Flow 2, DSR via AfterCast, with deletion + proof. |
| Audit | DPO quarterly review: Check tags, check access logs, check retention compliance, check DSR retraining, check model cards, check Bucket Policies, check SCPs. Evidence in `/evidence/`, RoPA Change Log, CloudTrail audit. |

---

## 10. Training and attestation

All staff must complete training on this policy before AFTER launch, and annually after that.

Training covers: 5 DataClass tags, how to choose, how to label, handling rules for Restricted-PII and Restricted-PII + Sensitive, no personal Gmail / Dropbox / WhatsApp / Sheet / QNAP / USB / GitHub for PII, how to report untagged data, how to report breach within 24h.

Attestation: Each staff member signs: I have read POL-DATA-CLASS-2025-01, I understand DataClass tags, I will NOT use personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, or GitHub public for PII, I will tell DPO if I find untagged data.

Training log: Kept by DPO, with date, attendance, attestation.

---

## 11. Appendix — Quick reference card

| If data contains... | Tag it... | Store it... | Share it... | Keep it... |
| :--- | :--- | :--- | :--- | :--- |
| No PII, public | `Public` | Any approved store | Publicly | Until superseded |
| No PII, internal | `Internal` | S3 Internal, eu-west-1 | Internal only | Per Retention Policy |
| No PII, business harm | `Confidential` | S3 Confidential, KMS, eu-west-1 | Internal + NDA, pre-signed URL 1h | 90d to 365d |
| Name, Email, Phone, Purchase History | `Restricted-PII` | S3 Prod, KMS, eu-west-1, Lake Formation sales-group only | Only on LuxeMart DPA, pre-signed URL 1h | 365d, logs proof 6y, PII NOT kept 6y |
| NIN, BVN, Passport, Bank Details | `Restricted-PII + Sensitive` | S3 HR, KMS, eu-west-1, Lake Formation hr-group only | HR + Finance only, WorkMail + pre-signed URL 1h | Rejected 180d, Hired 6y, proof 6y |

**Remember:** If unsure, classify higher. If you find untagged PII, stop, tag it, tell DPO. Do NOT forward it via personal Gmail, Dropbox, WhatsApp, Sheet, QNAP, USB, or GitHub.

**One fact, one file:** Retention details live once in `compliance/retention-policy.md`. Do NOT copy retention tables here. Link to Retention Policy.

---

## 12. Sign-off

| Role | Name | Date | Signature |
| :--- | :--- | :--- | :--- |
| DPO | Kenneth Pratt, Legal / Compliance | 18th October 2026 |  |
| Owner, Flow 1 | Funmi Daniels, Sales AE | 18th October 2026 |  |
| Owner, Flow 2 | Ifeoma Nnaji, People + Culture | 18th October 2026 |  |
| Approver, CEO | Aisha Bello, CEO | 18th October 2026 |  |

**Next review:** 18th April 2027, or earlier if new data type, new flow, breach, or new regulation.

