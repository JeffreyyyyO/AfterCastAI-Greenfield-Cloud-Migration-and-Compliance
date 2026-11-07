# Appendix C — Glossary

**File:** `docs/glossary.md`
**Repo:** AfterCastAI-Greenfield-Cloud-Migration-and-Compliance/

**Appendix:** C — Glossary
**Status:** Canonical definitions. One fact, one file. Other docs link here, do NOT copy definitions.

**Version:** 1.0, 18th October 2026

---

## Disclaimer: Lab Simulation and Fictional Addresses

All personal Gmail addresses in this file, for example funmi.personal@gmail.com, ifeoma.personal@gmail.com, bola.personal@gmail.com, are fictional. They were created for this AfterCast AI lab simulation only. They do not belong to real persons and do not correspond to real Gmail accounts. Do not attempt to send email to them. Any resemblance to a real person or a real account is coincidental. AfterCast AI is fictional. LuxeMart UK is fictional. All customer, applicant, and employee data in this file is simulated. No real personal data was used.

---

## How to use this glossary

This glossary is the single source of truth for terms in Audit Report 5, RoPA, DPIAs, Classification Policy, Retention Policy, Business Case, and Phase 1+ guides.

**Rule:**

- **1 Fact, 1 Name:** Character, Location, Count, DataClass must match exactly across Inventory, Diagram, Findings, RoPA, DPIA, and Terraform. If another doc disagrees with this glossary, this glossary + Audit Report 5 win, and the other doc must be fixed.
- **One fact, one file:** Glossary lives once here. Compliance mapping lives once in `compliance/compliance-mapping.md`, Appendix F. Other docs link, do not copy.
- **Do NOT invent new terms:** Do NOT use SPII, PII, Sensitive, Confidential-PII, or any other DataClass tag. Use only the 5 tags in Data Classification Policy. Do NOT invent new RetentionDays. Use only 90, 180, 365, 1825.

**How to link:** In other docs, write `See Glossary, docs/glossary.md, Appendix C, for [Term]` or link to this file. Do NOT paste the full definition into another doc.

---

## A

**AfterCast AI**
Fictional company, 3.5 years old, 30 staff, Yaba Lagos + London remote. AI-powered post-purchase forecasting for D2C brands — What will they buy next? Controller for Flow 2, Processor for Flow 1. See Audit Report 5, Sec 1.

**AFTER — To-Be — AWS Landing Zone — GDPR and NDPA Compliant — Single Copy KMS eu-west-1**
Target state after migration. 1 copy only in S3 KMS eu-west-1, per Flow. S3 Transfer Family SFTP mTLS + Lambda pseudonymisation + Lake Formation + VPC Endpoint + ECS Fargate + Aurora + Model Registry + Lifecycle + WorkMail + API Gateway + Cognito + WAF. See Audit Report 5, Sec 6, Business Case, `docs/0.6-cloud-business-case.md`, Sec 5.

**API Gateway**
AWS service, for `careers.aftercast.ai`, Flow 2 AFTER. API Gateway + Cognito + WAF, TLS 1.2, explicit consent checkbox, privacy notice at Form. No Google Form in AFTER.

**Applicant Hiring**
Correct title for Flow 2. Applicants are not employees. Flow 2 is Applicant Hiring, NOT Employee Onboarding. Flow 2 covers 143 applicants = 11 hired + 132 rejected.

**Art 22 — GDPR Art 22, Automated Decision-Making**
GDPR Article 22. If profiling produces legal or similarly significant effect, for example denying a discount, excluding from an offer, or setting a price, Art 22 applies. AfterCast must ask LuxeMart in DPA: Will forecast be used for Art 22 decisions? If yes, what safeguards, human review, right to object, right to contest? See DPIA-LuxeMart-Profiling, Sec 4.3.

**Art 28 — GDPR Art 28, Processor DPA**
GDPR Article 28. Processor must process only on documented instructions from Controller, with DPA, confidentiality, security, sub-processor approval, assist with DSR, assist with DPIA, delete or return at end, audit rights. AFTER requires DPA between LuxeMart UK and AfterCast AI before any Flow 1 intake.

**Art 30 — GDPR Art 30, RoPA**
GDPR Article 30. Record of Processing Activities. AfterCast RoPA is `compliance/RoPA.xlsx`, 4 activities: ROPA-01 Flow 1, ROPA-02 Flow 2 Rejected, ROPA-03 Flow 2 Hired, ROPA-04 DSR + Audit Logs.

**Art 32 — GDPR Art 32, Security**
GDPR Article 32. Security of processing: pseudonymisation, encryption, confidentiality, integrity, availability, resilience, testing. AFTER uses KMS, TLS 1.2, SSO + MFA, VPC Endpoint, Lake Formation, STS temp creds 1h, AWS Backup KMS + MFA Delete.

**Art 33 — GDPR Art 33, Breach 72h**
GDPR Article 33. Breach notification to supervisory authority within 72 hours. For Flow 1, AfterCast notifies LuxeMart within 24h internal, to allow LuxeMart 72h to ICO. For Flow 2, AfterCast notifies NDPC within 72h, per NDPA Sec 40.

**Art 35 — GDPR Art 35, DPIA**
GDPR Article 35. Data Protection Impact Assessment required before processing likely to result in high risk, including systematic profiling, Sensitive data, large-scale. Flow 1 DPIA is DPIA-LuxeMart-Profiling. Flow 2 DPIA is DPIA-HR-2025-01.

**Art 44 — GDPR Art 44, Transfers**
GDPR Article 44. Transfers to third countries. AFTER uses SCCs + TIA + DPA, eu-west-1 only, eu-west-2 backup/DR only, No US regions for PII.

**Aurora**
AWS Aurora, Postgres-compatible. Flow 1: `aftercast-prod-aurora-eu-west-1`, KMS, hashed ID only. Flow 2: `aftercast-prod-aurora-hr-eu-west-1`, KMS, AWS Backup KMS + MFA Delete. No local Postgres in AFTER.

**AWS Backup**
AWS service for backup, KMS, MFA Delete, Versioning. Retention = S3 Retention. Flow 1: 365 days. Flow 2 Rejected: 180 days. Flow 2 Hired: 6 years.

**AWS Organizations + SCPs**
AWS Organizations with Service Control Policies. Lab 1.1. SCPs deny non eu-west-1 / eu-west-2, enforce DataClass tagging, deny public S3, deny IAM without MFA. 5 accounts via Gmail plus-addressing.

---

## B

**BEFORE — As-Is — Legacy On-Prem + Shadow SaaS — Non-Compliant Baseline**
Starting state before migration. No public cloud. Small on-prem in-office infra + scattered shadow SaaS. 14 findings = 7 Critical + 7 High. 13 copies, 73,001 exposure points. See Audit Report 5, Sec 4 to Sec 7.

**BVN — Bank Verification Number**
Nigerian financial ID, Sensitive. Flow 2. NIN + BVN + Passport together allow bank fraud, SIM swap, loan application, full identity takeover. Per-record severity is higher than Flow 1. See DPIA-HR-2025-01, Sec 2.

**BVN List**
Email subject in BEFORE, Flow 2. Ifeoma Nnaji forwards Sheet as Excel via personal Gmail, subject `BVN List`, to Bola Tinubu. Subject line itself is breach. AFTER prohibits BVN List subject. Use WorkMail + pre-signed URL 1h, no PII in subject line.

---

## C

**careers.aftercast.ai**
Flow 2 AFTER intake, API Gateway + Cognito + WAF, TLS 1.2, explicit consent checkbox, privacy notice at Form. Replaces Google Form. No Google Form in AFTER.

**CloudTrail**
AWS service for audit logs. In `+audit` account, with Log Archive in `+log-archive`, with GuardDuty + Config + Macie. Logs kept 6 years in Glacier Deep Archive, PII NOT kept 6 years, hashed ID + DSR ID only.

**Cognito**
AWS Cognito, for `careers.aftercast.ai`, Flow 2 AFTER. Cognito + API Gateway + WAF, TLS 1.2, explicit consent checkbox.

**Confidential**
DataClass tag. Business confidential, no PII, harm if leaked. Examples: Pricing, contracts, vendor terms, roadmap, source code with no secrets, internal financial forecasts with no PII. Storage: S3 Confidential, KMS, eu-west-1. Retention: 90 days to 365 days, per document type. See Data Classification Policy, Sec 3.

**Controller**
Entity that determines purposes and means of processing. Flow 1 Controller: LuxeMart UK. Flow 2 Controller: AfterCast AI. Do NOT swap them. See RoPA, `compliance/RoPA.xlsx`.

**Control Tower**
AWS Control Tower, Lab 1.3. Log Archive + CloudTrail + GuardDuty + Config. In `+log-archive` + `+audit` accounts.

**Copies**
Number of persistent copies of personal data. BEFORE: Flow 1 6 copies, Flow 2 7 copies, Total 13 copies. AFTER: Flow 1 1 copy, Flow 2 1 copy, Total 2 copies, 1 per Flow. Copies count is persistent stores, not temp creds, not pre-signed URL 1h.

---

## D

**DataClass**
Data Classification tag. Only 5 tags exist: Public, Internal, Confidential, Restricted-PII, Restricted-PII + Sensitive. Do NOT use SPII, PII, Sensitive, Confidential-PII, or any other tag. See Data Classification Policy, `compliance/data-classification-policy.md`, Sec 3.

**Data Controller of Major Importance**
NDPA 2023 term. AfterCast AI is a Data Controller of Major Importance, because AI profiling, >10k subjects overall. Must appoint DPO, per NDPA Sec 32, and comply with NDPA Sec 29 RoPA, Sec 44 DPIA, Sec 41 Cross-Border, Sec 40 Breach 72h.

**DPA — Data Processing Agreement**
Agreement between Controller and Processor, GDPR Art 28, NDPA Sec 29. AFTER requires DPA between LuxeMart UK and AfterCast AI before any Flow 1 intake, with subject matter, duration, nature, purpose, types of data, obligations, sub-processors, breach 24h, DSR SLA, audit rights, no use for AfterCast own purposes.

**DPIA — Data Protection Impact Assessment**
Assessment required before processing likely to result in high risk, GDPR Art 35, NDPA Sec 44. Flow 1 DPIA: DPIA-LuxeMart-Profiling. Flow 2 DPIA: DPIA-HR-2025-01. Do NOT use one DPIA to cover the other Flow.

**DPO — Data Protection Officer**
Kenneth Pratt, Legal / Compliance. Reports to Aisha Bello, CEO, not Engineering. GDPR Art 37, NDPA Sec 32. Owns RoPA, DPIAs, Classification, Retention, Business Case, DPA + TIA, DSR, Breach playbook, Quarterly review.

**DSR — Data Subject Request**
Request for Access, Erasure, Portability, Objection, Withdrawal. GDPR Art 15, Art 17, NDPA Sec 35, Sec 36. AFTER: DSR API POST /dsr, with hashed ID, with retraining workflow for Flow 1, with deletion + proof for Flow 2. See DPIA-HR-2025-01 Sec 9 and DPIA-LuxeMart-Profiling Sec 9.

---

## E

**ECS Fargate**
AWS ECS Fargate, for Flow 1 + Flow 2 AFTER. ECS Fargate, STS temp creds 1h, VPC private subnets, no public internet, no local Postgres, no SELECT *. See Audit Report 5, Sec 6.

**Exposure Points**
Copies x Count. Flow 1: 12,000 x 6 = 72,000 points. Flow 2: 143 x 7 = 1,001 points. Total BEFORE: 73,001 points. AFTER: Flow 1 12,000 x 1 = 12,000, Flow 2 143 x 1 = 143, Total 12,143 points. Exposure points measure total exposure, not per-record severity. Flow 1 has higher total exposure, Flow 2 has higher per-record severity.

---

## F

**Findings — 14 Findings = 7 Critical + 7 High, 0 Medium**
Audit Report 5, Sec 7. Old 7 Critical, 4 High, 2 Medium = 13 is wrong.

**7 Critical Risks — Locked to Compliance Mapping Table 9:**
1. F1-02 — Flow 1 — Box 2 Gmail Inbox — Funmi personal Gmail, no MFA, 12k EU in US
2. F1-03 — Flow 1 — Box 3 Dropbox anyone-with-link US
3. F1-06 — Flow 1 — Box 6 GitHub model.pkl with raw emails — NOT F1-04
4. F2-01 — Flow 2 — Box 1 Google Form, no lawful basis for Sensitive
5. F2-03 — Flow 2 — Box 3 Google Sheet open to 30 staff US — NOT F2-02
6. F2-04 — Flow 2 — Box 4 BVN List email — NOT F2-03
7. F2-06 — Flow 2 — Box 6 QNAP + USB — NOT F2-05

**7 High Risks:**
F1-01 WeTransfer, F1-04 WhatsApp, F1-05 MacBook training, F1-07 Disposal, F2-02 Ifeoma Gmail ownership, F2-05 Dell, F2-07 Disposal

**Flow 1 — LuxeMart UK, 12k EU PII**
Box 1 LuxeMart UK → WeTransfer → Box 2 Funmi Daniels Sales AE, Personal Gmail Inbox → Manual download + upload to Dropbox → Box 3 Dropbox anyone-with-link US, 12k, no DataClass tag in BEFORE → Dropbox link via WhatsApp → Box 4 WhatsApp Group AfterCast Sales, 5 members + 2 ex-staff → Uncontrolled distribution → Box 5 Amara Eze DS, Downloads WhatsApp → MacBook → Jupyter SELECT * → Box 6 Local Postgres + model.pkl + GitHub public repo → Never deleted → Box 7 Disposal: Dropbox Trash + WhatsApp backup + GitHub history, Infinite — Should 365 days. Copies: 6. Exposure: 72,000 points.

**Flow 2 — HR, 143 NG NIN/BVN Sensitive**
Box 1 Job Applicants 143 → Google Form submits NIN/BVN/Passport → Box 2 Ifeoma Nnaji P&C, owns Form on personal Google account → Form auto-saves to Sheet, open edit → Box 3 Google Sheet AfterCast Hiring 2025 - Responses, open edit 30 staff, [Restricted-PII + Sensitive][NG][143] → Ifeoma attaches sheet in email subject BVN List → Box 4 Transmission: Personal Gmail Sent + Inbox — Ifeoma Sent + Bola Inbox + Google US — Box 4 is storage (house), Arrow D is transit (road) → Bola downloads to Finance Dell → Box 5 Bola Tinubu Finance → Copies Dell → QNAP + USB → Box 6 QNAP `//192.168.1.10/HR/CVs/` admin/admin + USB unlocked drawer → Never deleted → Box 7 Disposal: QNAP Old + Gmail Trash + USB, Infinite — Should 6 months / 180 days for rejected, 6 years for hired. Copies: 7. Exposure: 1,001 points. Total copies both flows: 13.

**Forensics Account**
AWS account for breach investigation, in `+audit` or separate, with CloudTrail + GuardDuty + Macie, with Object Lock, with DPO access only, with EventBridge to SNS to DPO.

---

## G

**GDPR — General Data Protection Regulation**
EU regulation. Key Articles in this repo: Art 5 Principles, Art 6 Lawful Basis, Art 9 Sensitive, Art 13 Transparency, Art 15 Access, Art 17 Erasure, Art 22 Automated Decisions, Art 25 By Design, Art 28 Processor DPA, Art 30 RoPA, Art 32 Security, Art 33 Breach 72h, Art 35 DPIA, Art 44 Transfers. Full texts in Appendix F, `compliance/compliance-mapping.md`.

**GitHub Public Repo**
BEFORE, Flow 1, Box 6. Public GitHub repo, `model.pkl` with raw emails. Model itself is PII. AFTER prohibits GitHub public for PII. Model Registry in AWS eu-west-1, private, hashed ID only, no model.pkl with raw emails.

**Glacier Deep Archive**
AWS S3 Glacier Deep Archive, for logs + deletion proof, 6 years, eu-west-1, KMS, Object Lock, hashed ID + DSR ID only, PII NOT kept 6 years.

**GuardDuty**
AWS GuardDuty, for threat detection, in all accounts, with CloudTrail + Config + Macie, with EventBridge to SNS to DPO.

---

## H

**Hashed ID — sha256_hashed_id**
Pseudonymised ID, `SHA256(email_lower_trimmed + salt)`, with salt in Secrets Manager, KMS, not in code, not in model, not in Aurora training table. Mapping table email → hashed_id stored separately, S3 KMS + Lake Formation, DPO + sales-group only for Flow 1, DPO + hr-group only for Flow 2, not DS. Training table uses hashed ID only. DSR erasure via hashed ID + retraining. See DPIA-LuxeMart-Profiling, Sec 9.

**Hired — 11 Hired**
Flow 2, 11 hired, of 143 total. Retention: 6 years, 1825 days, to Deep Archive. Post-Retention: 11 hired + 0 rejected (132 deleted).

---

## I

**IAM Identity Center**
AWS IAM Identity Center, Internal Directory, NOT Google Workspace. Lab 1.2. 30 users, Groups: sales-group, hr-group, ds-group, finance-group, plus DPO-Audit, SSO + MFA, built-in Identity Store. Simulated as connected to Google Workspace. No Google Workspace signup required.

**Internal**
DataClass tag. Internal docs, no PII, not for public. Examples: Internal runbooks, architecture diagrams with no PII, DSR logs with hashed ID only, meeting notes with no PII. Storage: S3 Internal, eu-west-1. Retention: Per Retention Policy, DSR logs + deletion proof 6 years, hashed ID only.

---

## K

**KMS — Key Management Service, CMK**
AWS KMS, Customer Managed Key. S3 KMS CMK, Aurora KMS, Secrets Manager KMS, AWS Backup KMS + MFA Delete, in eu-west-1. No KMS in BEFORE. KMS in AFTER for all PII.

---

## L

**Lake Formation**
AWS Lake Formation, for access control by DataClass + Group. Flow 1: sales-group only, columns `hashed_id, purchase_features` only, no SELECT *. Flow 2: hr-group only, finance-group for payroll only, mapping table DPO only.

**Lifecycle — 6 Lifecycle Phases**
Phases of data lifecycle, for 7-Node model. Phase 1 Collection [Box 1 + Transit A], Phase 2 Ingestion [Box 2 + Transit B], Phase 3 Distribution [Box 3 + Transit C], Phase 4 Access [Box 4 + Transit D], Phase 5 Replication [Box 5 + Transit E], Phase 6 Retention [Box 6 + Transit F + Box 7]. Total elements: 13 (7 Boxes + 6 Transits).

**Lifecycle — S3 Lifecycle**
AWS S3 Lifecycle, for auto-delete. Flow 1: Delete raw CSV after 365 days. Flow 2 Rejected: Glacier 30 days → Delete 180 days. Flow 2 Hired: Glacier 1 year → Deep Archive 6 years → Delete. Logs + proof to Glacier Deep Archive, kept 6 years, PII NOT kept 6 years.

**LuxeMart UK**
Fictional D2C brand, UK, Controller for Flow 1. Sends customer list to AfterCast AI for forecasting. 12,000 EU. See Flow 1.

---

## M

**Macie**
AWS Macie, for PII discovery, in `+audit` account, with CloudTrail + GuardDuty + Config, with S3 Inventory, with EventBridge to SNS to DPO.

**Mapping Table**
Table email → hashed_id, stored separately, S3 KMS + Lake Formation, DPO + sales-group only for Flow 1, DPO + hr-group only for Flow 2, not DS. DS never sees raw email. Deletion of mapping row on DSR + Retention expiry.

**Model.pkl**
Python pickle file for ML model. BEFORE, Flow 1, Box 6, model.pkl with raw emails, in public GitHub repo. Model itself is PII. AFTER prohibits model.pkl with raw emails. Model Registry hashed ID only, versioned, with model card, memorisation test, bias test, DSR retraining log.

**Model Registry**
AWS Model Registry, for Flow 1 AFTER. Model Registry hashed ID only, no raw email, no model.pkl with raw emails, versioned, with feature list, training date, hashed ID count, DSR retraining log, approval by DPO + DS.

---

## N

**NDPA 2023 — Nigeria Data Protection Act 2023**
Nigerian law. Key Sections in this repo: Sec 24 Principles (a-h), Sec 25 Lawful Basis, Sec 27 Privacy Notice, Sec 29 RoPA + Processor, Sec 32 DPO, Sec 35 Withdrawal of Consent (NOT DPIA, NOT minimisation), Sec 36 Right of Access, Sec 39 Security, Sec 40 Breach 72h, Sec 41 Cross-Border, Sec 44 DPIA (NOT Sec 28). Full texts in Appendix F.

**NIN — National Identification Number**
Nigerian national ID, Sensitive. Flow 2. NIN + BVN + Passport together allow identity takeover, bank fraud, SIM swap.

---

## P

**PII — Personal Data / Personally Identifiable Information**
Data that identifies a person, directly or indirectly. Flow 1: Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History. Flow 2: CV, NIN, BVN, Passport, Home Address, Bank Details. Do NOT use PII as a DataClass tag. Use Restricted-PII or Restricted-PII + Sensitive.

**Processor**
Entity that processes on behalf of Controller, only on documented instructions. Flow 1 Processor: AfterCast AI, for LuxeMart UK. Flow 2 Processor: AfterCast AI internal HR + Finance, for AfterCast AI as Controller. AWS is Sub-processor for AFTER.

**Pseudonymisation**
Technique, NOT a RetentionDays value, NOT declassification. Email → `sha256_hashed_id` with salt, before training, with mapping separate. Pseudonymised data is still personal data, still Restricted-PII, until truly anonymised and re-identification is impossible. See DPIA-LuxeMart-Profiling, Sec 9.

**Public**
DataClass tag. Public marketing, no PII, can be published. Examples: Public website copy, public blog, public job advert with no applicant data.

---

## Q

**QNAP NAS**
QNAP NAS, `//192.168.1.10/HR/CVs/`, admin/admin, SMBv1, no encryption, no backup, no access log, Yaba Office Server Closet. BEFORE, Flow 2, Box 6. AFTER: Decommissioned + wiped, no QNAP for PII, S3 HR KMS + AWS Backup KMS + MFA Delete.

---

## R

**Rejected — 132 Rejected**
Flow 2, 132 rejected, of 143 total. Retention: 180 days, 6 months, Glacier 30 days → Delete 180 days. Post-Retention: 0 rejected kept. BEFORE kept 132 forever, should have been deleted after 180 days.

**RetentionDays**
Number of days data is kept. Only 90, 180, 365, 1825 exist for personal data. 90 = Internal logs, temp files. 180 = Flow 2 Rejected. 365 = Flow 1 Raw CSV. 1825 = Flow 2 Hired + Logs + Deletion Proof, 6 years. Pseudonymisation is a technique, NOT a RetentionDays value. See Retention Policy, `compliance/retention-policy.md`, Sec 3.

**Restricted-PII**
DataClass tag. Customer PII, EU, identifiable, harm if leaked is phishing, fraud, spam. Flow 1: LuxeMart UK, 12,000 EU, Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History. Storage: S3 Prod KMS eu-west-1, Lake Formation sales-group only. Retention: 365 days.

**Restricted-PII + Sensitive**
DataClass tag. Applicant / employee PII + Sensitive, NIN, BVN, Passport, Bank Details, harm if leaked is identity takeover, bank fraud, SIM swap. Flow 2: HR Applicants, 143 NG, CV, NIN, BVN, Passport, Home Address, Bank Details. Storage: S3 HR KMS eu-west-1, Lake Formation hr-group only. Retention: Rejected 180 days, Hired 6 years. Do NOT use SPII. Use Restricted-PII + Sensitive.

**RoPA — Record of Processing Activities**
GDPR Art 30, NDPA Sec 29. AfterCast RoPA is `compliance/RoPA.xlsx`, 4 activities, BEFORE vs AFTER in same row, with Transfers + Retention sheets.

---

## S

**S3 — Simple Storage Service**
AWS S3. Flow 1: `s3://aftercast-prod-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Block Public, Versioning, Lake Formation sales-group only. Flow 2: `s3://aftercast-hr-restricted-pii-eu-west-1/`, KMS CMK, eu-west-1, Block Public, Versioning, Lake Formation hr-group only.

**SCCs — Standard Contractual Clauses**
GDPR Art 46, for transfers to third countries. AFTER uses SCCs + TIA + DPA, for LuxeMart UK to AfterCast AI, and for AfterCast AI to AWS, eu-west-1 only. BEFORE had no SCCs.

**SCPs — Service Control Policies**
AWS SCPs, Lab 1.1, in AWS Organizations. Deny non eu-west-1 / eu-west-2, enforce DataClass tagging, deny public S3, deny IAM without MFA.

**Secrets Manager**
AWS Secrets Manager, KMS, for salt for `sha256_hashed_id`, for API keys, for passwords. Salt stored in Secrets Manager, KMS, not in code, not in model, not in Aurora training table. No secrets in email, no secrets in GitHub, no secrets in notebooks.

**Sensitive — Sensitive Personal Data**
NIN, BVN, Passport, Bank Details, National ID, Financial ID, Health, etc. In this repo, Sensitive is NIN, BVN, Passport, Bank Details, Flow 2 only. No health info in scope. Do NOT use Sensitive as a DataClass tag alone. Use Restricted-PII + Sensitive.

**7-Node Model**
Model for data flow, 7 Boxes + 6 Transits = 13 elements. Boxes: Box 1 SOURCE, Box 2 ACTOR, Box 3 STORAGE 1, Box 4 TRANSMISSION, Box 5 ACTOR 2, Box 6 STORAGE 2, Box 7 DISPOSAL. Transits: Transit A to F. Box = WHERE data lives (storage). Arrow = WHO moved it and HOW (transit). Box 4 vs Arrow D: Box 4 = Sent + Inbox storage, Arrow D = Bola download action. See Audit Report 5, Sec 3.

**SPII**
Do NOT use SPII. Use Restricted-PII + Sensitive. SPII is not a DataClass tag in this repo.

**SSO + MFA — Single Sign-On + Multi-Factor Authentication**
IAM Identity Center, SSO + MFA, for all staff, 30 users, Groups: sales-group, hr-group, ds-group, finance-group, plus DPO-Audit. No SSO + MFA in BEFORE. SSO + MFA in AFTER for all access to PII.

**STS Temp Creds 1h**
AWS STS temporary credentials, 1 hour, for ECS Fargate, for pre-signed URL 1h, for WorkMail, for DSR, for migration. No long-lived credentials for PII. No API keys in email, no API keys in GitHub, no API keys in notebooks.

---

## T

**TIA — Transfer Impact Assessment**
Assessment for transfers to third countries, GDPR Art 44 + Art 46, NDPA Sec 41. AFTER requires TIA for LuxeMart UK to AfterCast AI, and for AfterCast AI to AWS, eu-west-1 only, covering Nigeria law, eu-west-1 law, technical + organisational measures, access by authorities, proportionality. BEFORE had no TIA.

**Transfer Family — S3 Transfer Family SFTP mTLS**
AWS S3 Transfer Family, SFTP with mutual TLS, for Flow 1 AFTER intake. LuxeMart client cert, IP allowlist, TLS 1.2, S3 Event to Lambda. Replaces WeTransfer. No WeTransfer in AFTER.

---

## V

**VPC — Virtual Private Cloud**
AWS VPC, for AFTER. Hub `10.10.0.0/16` in `10.0.0.0/8`, private subnets, VPC Endpoint PrivateLink, no public internet for PII, NAT for dev only. Old on-prem `192.168.1.0/24` is BEFORE, not AFTER.

**VPC Endpoint — PrivateLink**
AWS VPC Endpoint, PrivateLink, for S3 + Aurora + Lambda, no public internet, for PII. See Audit Report 5, Sec 6.

---

## W

**WAF — Web Application Firewall**
AWS WAF, for `careers.aftercast.ai`, Flow 2 AFTER. API Gateway + Cognito + WAF, TLS 1.2, explicit consent checkbox.

**WeTransfer**
BEFORE, Flow 1, Transit A. WeTransfer link, no encryption, no password expiry, no DPA, no SCCs, no TIA. AFTER prohibits WeTransfer for PII. Use SFTP mTLS.

**WorkMail**
AWS WorkMail, corporate email, `firstname.lastname@aftercast.ai`, 30 users, SSO + MFA, pre-signed URL 1h, no personal Gmail. Company mail is WorkMail. `aftercast.ai@gmail.com` is NOT Company Gmail. It is a Gmail account with username aftercast.ai. Do NOT use it as Company Gmail.

**WhatsApp Group — AfterCast Sales**
BEFORE, Flow 1, Box 4. WhatsApp Group AfterCast Sales, 5 members + 2 ex-staff, media auto-saves to phones, no audit, no revoke, no DPA. AFTER prohibits WhatsApp for PII. Use WorkMail + pre-signed URL 1h + SFTP mTLS.

---

## Staff — Canonical

| Name | Role | Flow | Notes |
| :--- | :--- | :--- | :--- |
| Aisha Bello | CEO | All | Approver, DPO reports to Aisha Bello, not Engineering |
| David Chen | CTO, Co-founder | Engineering | CTO, Co-founder, not Executive |
| Tolu Adeyemi | COO | All | COO, full name, not Tolu Ade |
| Funmi Daniels | Sales AE | Flow 1, Box 2 | Sales AE, NOT Customer Success, NOT P&C/HR. Personal Gmail: funmi.personal@gmail.com — Funmi Daniels (Sales, AE). WorkMail: funmi.daniels@aftercast.ai — Funmi Daniels (Sales, AE) |
| Amara Eze | Data Science | Flow 1, Box 5 | Data Science, Box 5 Flow 1, Downloads WhatsApp → MacBook |
| Ifeoma Nnaji | People & Culture (P&C/HR) | Flow 2, Box 2 | P&C/HR, Box 2 Flow 2, Owns Google Form on ifeoma.personal@gmail.com. Personal Gmail: ifeoma.personal@gmail.com — Ifeoma Nnaji (P&C/HR). WorkMail: ifeoma.nnaji@aftercast.ai — Ifeoma Nnaji (P&C/HR) |
| Bola Tinubu | Finance | Flow 2, Box 5 | Finance, Box 5 Flow 2, Finance Dell. Personal Gmail: bola.personal@gmail.com — Bola Tinubu (Finance). WorkMail: bola.tinubu@aftercast.ai — Bola Tinubu (Finance) |
| Kenneth Pratt | Legal / Compliance, DPO | All | DPO, GDPR Art 37, NDPA Sec 32, reports to Aisha Bello, not Engineering |
| Emeka Nwosu | Engineering | Engineering | Engineering, MacBook prod dump is out of scope, unverified. Do NOT attribute Flow 1 prod dump to Emeka. |
| Chloe Williams | Marketing Lead | Out of scope | Marketing Lead, personal Drive is out of scope |
| Grace Okafor | UX Researcher | Out of scope | UX Researcher, Figma is out of scope |
| Ruth Adamu | Admin Officer | Out of scope | Admin Officer, Admin Dell is separate from Finance Dell |
| Jeffrey Obi | Cloud Security Engineer | All | Owner, Business Case, Terraform, Migration |

**Email format — Canonical, em-dash separator:**
`Personal Gmail: funmi.personal@gmail.com — Funmi Daniels (Sales, AE)` and `WorkMail: funmi.daniels@aftercast.ai — Funmi Daniels (Sales, AE)`. Use em-dash everywhere. Do NOT change without asking.

**Counts — Canonical:**
- Flow 1: 12,000 EU, 6 copies, 72,000 points
- Flow 2: 143 total = 11 hired + 132 rejected, 7 copies, 1,001 points
- Total: 13 copies, 73,001 points
- Findings: 14 = 7 Critical + 7 High, 0 Medium
- NEVER write 143+132 = 275.

---

## Phases & Labs — Corrected Count: 11 Phases, 45 Labs, NOT 58

- Phase 0: Discovery & Privacy Foundation — COMPLETE
- Phase 0.5: Compliance Artifacts — RoPA, DPIA-HR, DPIA-LuxeMart, Classification + Retention, Business Case
- Phase 1: Landing Zone Foundation — Lab 1.1 Organizations + SCPs, Lab 1.2 IAM Identity Center, Lab 1.3 Control Tower + Log Archive + CloudTrail + GuardDuty + Config
- Phase 2: Data Protection & Storage — KMS, S3, Lake Formation, WorkMail, Lifecycle, Deletion proof
- Phase 3: Network & Compute — VPC Hub, PrivateLink, ECS Fargate, Aurora, API Gateway, Cognito, WAF
- Phase 4: Migration & Cutover — Transfer Family, Lambda pseudonymisation, Migration, Cutover, Deletion of BEFORE copies
- Phase 5: Operations, Monitoring & Evidence — Monitoring, Evidence, Prowler, Appendix G + H
- Phases 6-10 in roadmap: Security, Compliance, Cost, Final Report — details in `00-prerequisites.md`

---

## Appendixes — Where each appendix lives

| Appendix | Title | File |
| :--- | :--- | :--- |
| Appendix A | Data Flow Diagrams | Same 4 diagrams as body Sec 6, full-page, same files, no new diagrams. Fix Flow 1 BEFORE GitHub copy and Flow 2 BEFORE count in both places. |
| Appendix B | Evidence Pack | NOT started. Needs screenshots: QNAP panel, Dropbox link, Gmail BVN List header, Sheet sharing, Form no consent, GitHub repo, USB drawer. |
| Appendix C | Glossary | This file, `docs/glossary.md` |
| Appendix D + E | Shadow IT Inventory + Out of Scope Observations | `docs/0.1-shadow-it-inventory.md` |
| Appendix F | Compliance Mapping | `compliance/compliance-mapping.md` |
| Appendix G | Lab Evidence Index | `docs/appendix-g-lab-evidence-index.md` — NOT started |
| Appendix H | Before vs After Evidence | `docs/appendix-h-before-vs-after.md` — NOT started. Prowler before vs after to be measured in Phase 3, NOT 87% vs 5%. |

