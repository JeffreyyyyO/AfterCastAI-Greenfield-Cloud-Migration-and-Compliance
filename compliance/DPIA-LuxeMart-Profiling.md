# DPIA-LuxeMart-Profiling - Data Protection Impact Assessment

**File:** `compliance/DPIA-LuxeMart-Profiling.md`
**Repo:** AfterCastAI-Greenfield-Cloud-Migration-and-Compliance/

**Processing:** AI Profiling and Forecasting, LuxeMart UK Customer Data
**Flow:** Flow 1, LuxeMart UK, 12,000 EU
**Status:** Required BEFORE processing. This DPIA covers Flow 1 profiling only.

---

## Disclaimer: Lab Simulation and Fictional Addresses

All personal Gmail addresses in this file, for example funmi.personal@gmail.com, are fictional. They were created for this AfterCast AI lab simulation only. They do not belong to real persons and do not correspond to real Gmail accounts. Do not attempt to send email to them. Any resemblance to a real person or a real account is coincidental. AfterCast AI is fictional. LuxeMart UK is fictional. All customer data in this file is simulated. No real personal data was used.

---

## 1. Document control

| Item | Fact |
| :--- | :--- |
| DPIA ID | DPIA-LuxeMart-Profiling |
| Title | AI Profiling and Forecasting, LuxeMart UK Customer Data |
| Version | 1.0, 18th October 2026 |
| Controller | LuxeMart UK, UK D2C brand, data controller |
| Processor | AfterCast AI, Yaba Lagos + London, data processor for LuxeMart |
| Data Controller of Major Importance | Yes, NDPA 2023. AI profiling, >10k subjects overall. |
| Sub-processor AFTER | AWS, S3, Lambda, ECS, Aurora, in eu-west-1 only for PII |
| DPO AfterCast AI | Kenneth Pratt, Legal / Compliance. Reports to Aisha Bello, CEO, not Engineering. GDPR Art 37, NDPA Sec 32. |
| DPO LuxeMart UK | To be confirmed by LuxeMart, via DPA. AfterCast cannot sign off on behalf of LuxeMart. |
| Owner AfterCast AI | Funmi Daniels, Sales AE, Box 2 Flow 1, client intake |
| Second owner | Amara Eze, Data Science, Box 5 Flow 1, model training |
| Approver AfterCast AI | Aisha Bello, CEO |
| Related docs | Audit Report 5, `docs/0.2-security-audit-report.md`, Sec 7 F1-01 to F1-07. RoPA, `compliance/RoPA.xlsx`. Appendix F, `compliance/compliance-mapping.md`. Appendix A, Flow 1 Diagrams. DPIA-HR-2025-01, `compliance/DPIA-HR-2025-01.md`, for Flow 2 only. |
| Review date | 18th April 2027, or earlier if model changes, new data source, new purpose, breach occurs, or LuxeMart changes instructions |
| DPIA required by | GDPR Art 35, NDPA Sec 44 |

**Why a DPIA is required:** This processing is likely to result in high risk. It is systematic and extensive profiling, AI forecasting what a person will buy next, large-scale EU data, 12,000 records, with model memorisation, re-identification, and automated profiling that can affect marketing and offers. GDPR Art 35(1) and NDPA Sec 44 both require a DPIA before processing. No DPIA existed in BEFORE. This DPIA corrects that.

**Who owns this DPIA:** LuxeMart UK as controller owns the controller DPIA. AfterCast AI as processor owns this processor DPIA and must assist LuxeMart under GDPR Art 28(3)(f) and NDPA Sec 29. This file is AfterCast AI processor DPIA. It does not replace LuxeMart controller DPIA.

---

## 2. Executive summary

**Decision: High risk in BEFORE. High risk can be reduced to Medium residual risk in AFTER, only if all AFTER controls in Sec 8 are implemented. Do NOT continue BEFORE processing. Do NOT train a model on raw emails.**

BEFORE collected LuxeMart CSV via WeTransfer, stored in Funmi personal Gmail, Dropbox anyone-with-link US, WhatsApp group with ex-staff, Amara MacBook, local Postgres, and public GitHub `model.pkl` with raw emails. Copies: 6. Exposure: 72,000 points. Findings: F1-01 to F1-07, 3 Critical + 4 High.

The most severe risks are not just breach. They are profiling harm and model memorisation:

- Profiling: AfterCast predicts what a person will buy next. That profile can be used to target, nudge, price, or exclude. If the profile is wrong, the person is misclassified. If the profile leaks, the person is exposed.
- Model memorisation: `model.pkl` trained on raw emails memorises emails. The model file itself becomes PII. Deleting the CSV does not delete the model. Anyone with the model can extract emails.
- Re-identification: Even if name is removed, email + purchase history re-identifies. Pseudonymisation must be done before training, not after.

AFTER fixes this by: SFTP mTLS intake, Lambda validate + classify + pseudonymise email to `sha256_hashed_id` before any training, Lake Formation sales-group only, VPC Endpoint PrivateLink, ECS Fargate STS temp creds 1h, Aurora with hashed ID only, Model Registry with hashed ID only, raw CSV deleted after 365 days, logs kept 6y in Glacier Deep Archive with PII NOT kept 6y, DSR erasure via hashed ID + retraining.

Residual risk remains Medium, because profiling is inherently high risk, even with pseudonymisation. Residual risk is acceptable only with DPO oversight, model governance, quarterly review, and LuxeMart DPA + TIA in place.

---

## 3. Description of processing

### 3.1 Purpose

Predict what a LuxeMart customer will buy next, to improve forecasting, stock, and marketing. Purpose is limited to forecasting as instructed by LuxeMart in DPA. AfterCast must NOT use LuxeMart data for its own purposes, for other clients, for advertising, or for model improvement outside the DPA.

### 3.2 What is NOT the purpose

- AfterCast must NOT profile for credit, insurance, employment, health, or any decision with legal or similarly significant effect, unless LuxeMart explicitly instructs it and documents a separate DPIA and Art 22 assessment.
- AfterCast must NOT sell, share, or re-use LuxeMart data.
- AfterCast must NOT train a shared foundation model on LuxeMart data and then use that model for other clients, unless LuxeMart explicitly authorises it and pseudonymisation + separation are proven.

### 3.3 Data subjects

LuxeMart UK customers, 12,000 EU data subjects.

- Location: EU, primarily UK, per Audit Sec 4.
- DataClass: Restricted-PII, EU.
- Vulnerability: Customers did not directly give data to AfterCast. They gave it to LuxeMart. They may not know AfterCast exists. Transparency via LuxeMart privacy notice is essential.

### 3.4 Personal data

| Data | Category | Used for training? | AFTER treatment |
| :--- | :--- | :--- | :--- |
| Name | PII | No | Pseudonymised, not used for training, stored in S3 KMS only if needed for DSR mapping, separate table |
| Email | PII, direct identifier | No, raw email must NOT be used for training | Hashed to `sha256_hashed_id` with salt, before training. Raw email in S3 KMS only, not in Aurora training table, not in model. |
| Phone | PII | No | Pseudonymised or removed, not used for training |
| Delivery Address | PII, location | No, raw address must NOT be used for training | Generalised to region / postcode district, or removed, before training |
| Loyalty ID | PII, pseudonymous ID | Yes, as `hashed_id` only | Hashed to `sha256_hashed_id`, used as join key, not raw ID |
| Purchase History | PII, behavioural | Yes, as pseudonymised features | Purchase history with `hashed_id` only, no name, no email, no phone, no address |

**Correction from BEFORE:** BEFORE used `SELECT *` in Jupyter, with email as `user_id`, and trained on raw emails. AFTER uses `SELECT hashed_id, purchase_features` only. Raw email is never a training feature.

### 3.5 BEFORE flow, 7-Node, summary

Full diagram in Appendix A, Flow 1 BEFORE.

| Node | Location / Owner | Copies |
| :--- | :--- | :--- |
| Box 1 Source | LuxeMart UK | 1 source |
| Transit A | WeTransfer link, no encryption | — |
| Box 2 Actor 1 | Personal Gmail: funmi.personal@gmail.com — Funmi Daniels (Sales, AE), Gmail Inbox | Copy 1 |
| Transit B | Manual download + upload | — |
| Box 3 Storage 1 | Personal Dropbox: funmi.personal.dropbox.com — Funmi Daniels (Sales, AE), anyone-with-link US, 12,000, no DataClass tag in BEFORE | Copy 2 |
| Transit C | Dropbox link via WhatsApp | — |
| Box 4 Transmission | WhatsApp Group AfterCast Sales, 5 members + 2 ex-staff | Copy 3 |
| Transit D | Uncontrolled distribution, no audit, no revoke | — |
| Box 5 Actor 2 | Amara Eze (Data Science), Downloads WhatsApp to MacBook | Copy 4 |
| Transit E | Jupyter SELECT *, email as user_id | — |
| Box 6 Storage 2 | Local Postgres + model.pkl + GitHub public repo, raw emails in model | Copy 5 + 6 |
| Transit F | Never deleted | — |
| Box 7 Disposal | Dropbox Trash + WhatsApp backup + GitHub history, Infinite | Infinite |

Copies BEFORE: 6. Exposure: 72,000 points. GitHub copy is Copy 6, often missed in v1 diagrams. It must be counted.

### 3.6 AFTER flow, 7-Node, summary

Full diagram in Appendix A, Flow 1 AFTER.

| Node | Location / Owner | Copies |
| :--- | :--- | :--- |
| Box 1 Source | LuxeMart UK via SFTP | 1 source |
| Transit A | S3 Transfer Family SFTP mTLS, TLS 1.2, LuxeMart client cert, IP allowlist | Encrypted, authenticated |
| Box 2 Actor 1 | Funmi Daniels via Identity Center SSO + MFA, WorkMail funmi.daniels@aftercast.ai, no personal Gmail | No personal copy |
| Transit B | Lambda validate + classify + pseudonymise, S3 Event, no manual download | Automated, logged |
| Box 3 Storage 1 | S3 s3://aftercast-prod-restricted-pii-eu-west-1/, KMS CMK, eu-west-1, Block Public, Versioning, Lake Formation sales-group only | Copy 1, single copy |
| Transit C | VPC Endpoint PrivateLink, no public internet, STS temp creds 1h | Private, temp |
| Box 4 Transmission | ECS Fargate, STS temp creds 1h, VPC private subnets, no WhatsApp, no Dropbox | Temp, no persistent copy |
| Transit D | Aurora write, hashed ID only | — |
| Box 5 Actor 2 | Amara Eze via Identity Center SSO + MFA, DS group, corp laptop, no WhatsApp download | No local copy |
| Transit E | Aurora query, SELECT hashed_id, purchase_features, no SELECT * | — |
| Box 6 Storage 2 | Aurora aftercast-prod-aurora-eu-west-1, KMS, hashed ID only + Model Registry hashed ID only, no raw email, no model.pkl with raw emails | Single copy, pseudonymised |
| Transit F | Lifecycle auto-delete | — |
| Box 7 Disposal | S3 Inventory + CloudTrail + Macie proof. Raw CSV deleted after 365 days. Logs kept 6y in Glacier Deep Archive, PII NOT kept 6y. Model retrained without deleted hashed IDs. | Post-Retention: 0 raw PII after 365 days, except logs proof with no PII |

Copies AFTER: 1.

**Clarification on 6y:** Customer PII is deleted after 365 days. Logs + deletion proof are kept 6y for audit. PII is NOT kept 6y. If a log contains PII, it must be redacted or pseudonymised before it is kept 6y.

---

## 4. Lawful basis, necessity, proportionality

### 4.1 Lawful basis

| Processing | GDPR | NDPA |
| :--- | :--- | :--- |
| LuxeMart collecting customer data for forecasting | Art 6(1)(b) Contract with customer, or Art 6(1)(f) Legitimate Interests, as documented by LuxeMart in its own DPIA and privacy notice. AfterCast does NOT decide this basis. | Sec 25 Contract or Legitimate Interests, as documented by LuxeMart. |
| AfterCast processing on behalf of LuxeMart | Art 6(1)(b) Contract via DPA, Art 28 Processor. AfterCast acts only on documented instructions from LuxeMart. | Sec 25 Contract via DPA, Sec 29 Processor. AfterCast acts only on LuxeMart instructions. |
| Profiling, forecasting what a person will buy next | Art 6 + Art 22, if profiling produces legal or similarly significant effect. Must be assessed by LuxeMart. AfterCast must flag Art 22 risk to LuxeMart. | Sec 25 + Sec 44 DPIA, plus Sec 27 Transparency. Profiling must be disclosed in LuxeMart privacy notice. |
| Pseudonymisation, security | Art 5(1)(f) + Art 32, Art 25 By Design | Sec 24 + Sec 39, Sec 44 |
| Transfers to AfterCast in Nigeria, if any | Art 44 + Art 46, SCCs + TIA + DPA, see Sec 6 | Sec 41 Cross-Border, adequate safeguards + DPA |

**What is NOT a lawful basis:** Legitimate Interest + Consent alone is NOT enough for AfterCast as processor. AfterCast needs a DPA with LuxeMart, with documented instructions, plus LuxeMart's own lawful basis with customers. BEFORE had no DPA, no SCCs, no documented instructions. AFTER requires DPA before any new intake.

### 4.2 Necessity test

| Question | Answer |
| :--- | :--- |
| Is raw email needed to forecast? | No. Hashed ID is enough to join purchases over time. |
| Is name needed to forecast? | No. Name is not a forecasting feature. |
| Is phone needed to forecast? | No. Phone is not a forecasting feature. |
| Is full delivery address needed? | No. Region or postcode district is enough, if location is needed at all. |
| Is full purchase history needed? | Yes, but only as pseudonymised features, with minimisation: last 12 months, or as instructed by LuxeMart, not infinite history. |
| Could we achieve purpose with less data? | Yes. Pseudonymise before training, minimise features, delete raw CSV after 365 days. That is AFTER design. |

**Proportionality conclusion:** BEFORE was disproportionate. It collected raw identifiers, kept them forever, in 6 copies, and trained on raw emails. AFTER is proportionate. It pseudonymises before training, keeps raw CSV only 365 days, single copy, and trains only on hashed features.

### 4.3 Automated decision-making, Art 22

AfterCast forecasting is profiling. If LuxeMart uses the forecast to make a decision that produces legal or similarly significant effect, for example denying a discount, excluding from an offer, or setting a price, then GDPR Art 22 applies.

AfterCast must:

- Ask LuxeMart in DPA: Will forecast be used for Art 22 decisions? If yes, what safeguards, human review, right to object, and right to contest?
- Log model version, features, and `hashed_id` for each forecast, so a decision can be explained and contested.
- NOT claim the model is fully automated and unchallengeable. Provide human review path via LuxeMart.
- If LuxeMart says no Art 22 decisions, document that in DPA and re-assess if purpose changes.

BEFORE had no Art 22 assessment. AFTER requires Art 22 assessment in DPA before intake.

### 4.4 Transparency

BEFORE: Customers did not know AfterCast existed. No transparency.

AFTER: LuxeMart must update its privacy notice to name AfterCast AI as processor, state purpose forecasting, state profiling, state retention 365 days, state rights, state transfers to Nigeria if any, with SCCs + TIA summary. AfterCast must provide LuxeMart with processor transparency text, to be included in LuxeMart notice.

---

## 5. Consultation

| Who | When | What was asked | Outcome |
| :--- | :--- | :--- | :--- |
| DPO, Kenneth Pratt | Before DPIA, Oct 2026 | Is DPIA required? Is training on raw emails allowed? | Yes, DPIA required. No, training on raw emails is not allowed. Pseudonymise before training. |
| Data Science, Amara Eze | Oct 2026 | Why is email used as user_id? Can model train on hashed ID? | Amara agreed hashed ID is enough. Email as user_id was convenience, not necessity. |
| Sales, Funmi Daniels | Oct 2026 | Why is WeTransfer + Dropbox + WhatsApp used? What does LuxeMart expect? | Funmi agreed SFTP mTLS is required. LuxeMart expects DPA + secure intake. |
| Engineering / Cloud Security | Oct 2026 | Can we build SFTP mTLS + Lambda pseudonymisation + Lake Formation? | Yes. See AFTER flow in Sec 3.6. |
| LuxeMart UK | To be confirmed via DPA | What is LuxeMart lawful basis? Will forecast be used for Art 22 decisions? What is retention? What is TIA position? | AFTER intake must NOT start until LuxeMart answers in DPA. See Sec 6. |
| Data subjects, customers | Not consulted directly. Transparency via LuxeMart privacy notice. | Customers were not given a chance to object in BEFORE. | AFTER includes LuxeMart privacy notice update + DSR via hashed ID. Future consultation: LuxeMart to sample customers after AFTER launch, to test notice clarity. |

No consultation with NDPC or ICO was required before this DPIA, because residual risk after mitigations is Medium, not High, and no new technology with unmitigated high risk is proposed. If residual risk becomes High, or if Art 22 decisions are introduced, DPO must consult NDPC / ICO before processing, per GDPR Art 36, NDPA Sec 44.

---

## 6. Processor obligations, DPA, TIA, transfers

### 6.1 DPA, GDPR Art 28, NDPA Sec 29

AFTER intake requires a signed DPA between LuxeMart UK and AfterCast AI, with:

- Subject matter, duration, nature, purpose: Forecasting, 12,000 EU, 365 days
- Types of personal data: Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History, as pseudonymised per Sec 3.4
- Obligations: Process only on documented instructions, confidentiality, security Art 32, sub-processor approval, assist with DSR, assist with DPIA, delete or return at end, audit rights
- Sub-processors: AWS in eu-west-1, with SCCs if needed, listed in DPA
- Breach: Notify LuxeMart without undue delay, within 24h internal, to allow LuxeMart to meet 72h to ICO
- No use for AfterCast own purposes

BEFORE had no DPA. AFTER intake must NOT start without DPA.

### 6.2 Transfers, GDPR Art 44, NDPA Sec 41

**Residency rule:** Primary eu-west-1, backup eu-west-2 only for DR. No US regions for PII. No personal Gmail, no Dropbox US, no WhatsApp US backup.

**Transfer analysis:**

| Transfer | BEFORE | AFTER |
| :--- | :--- | :--- |
| LuxeMart UK to AfterCast AI | WeTransfer, no encryption, no SCCs, no TIA | SFTP mTLS, TLS 1.2, DPA + SCCs + TIA |
| AfterCast AI to AWS | No DPA, no region control | AWS DPA + SCCs, eu-west-1 only, KMS CMK, VPC Endpoint |
| AfterCast AI to Dropbox US | Personal Dropbox, US, no safeguards | Prohibited, no Dropbox |
| AfterCast AI to WhatsApp | WhatsApp, US backup, no safeguards | Prohibited, no WhatsApp |
| AfterCast AI to GitHub | Public GitHub, US, no safeguards | Prohibited, Model Registry in AWS eu-west-1, private, hashed ID only |

**TIA, Transfer Impact Assessment:** Must be completed before AFTER intake, with LuxeMart, covering:

- Law in Nigeria and in eu-west-1, access by authorities, proportionality
- Technical measures: KMS, TLS 1.2, pseudonymisation, VPC Endpoint, no US PII
- Organisational measures: DPA, access control, audit, breach playbook
- If TIA finds high risk that cannot be mitigated, do NOT proceed with transfer. Re-assess residency or processing location.

BEFORE had no TIA. AFTER requires TIA.

### 6.3 Sub-processors

AWS is sub-processor. AfterCast must:

- List AWS in DPA, with eu-west-1 only
- Ensure AWS DPA + SCCs are in place
- Ensure no other sub-processor is added without LuxeMart approval
- Ensure no personal SaaS, no Dropbox, no Mailchimp, no WhatsApp, is used as sub-processor without DPA

---

## 7. Risk assessment - BEFORE

Likelihood: Low, Medium, High. Severity: Low, Medium, High, Critical. Risk = Likelihood x Severity.

| ID | Risk to data subjects | Likelihood BEFORE | Severity | Risk BEFORE | Linked findings |
| :--- | :--- | :--- | :--- | :--- | :--- |
| R1 | Phishing, identity theft via leaked email + name + phone + purchase history. Per-record harm is phishing, lower than Flow 2, but total exposure is higher. | High, 6 copies, Dropbox anyone-with-link, WhatsApp ex-staff, GitHub public | High, phishing, account takeover, spam, fraud | Critical | F1-02, F1-03, F1-04, F1-06 |
| R2 | Profiling harm, misclassification, exclusion, price discrimination, if forecast is wrong or misused | High, model trained on raw data, no governance, no Art 22 assessment | High, unfair treatment, loss of offers, discrimination risk | Critical | F1-05, F1-06 |
| R3 | Model memorisation, model.pkl leaks raw emails, re-identification via model | High, model.pkl with raw emails in public GitHub | Critical, model itself is PII, deletion of CSV does not delete model | Critical | F1-06 |
| R4 | Re-identification via pseudonymisation failure, if pseudonymisation is done after training or not at all | High, no pseudonymisation in BEFORE | High, email + purchase history re-identifies | Critical | F1-05, F1-06 |
| R5 | Unauthorised access by ex-staff, outsiders via Dropbox link, WhatsApp, GitHub | High, 2 ex-staff in WhatsApp, Dropbox link forwardable, GitHub public | High, 12,000 records exposed | Critical | F1-03, F1-04, F1-06 |
| R6 | Inability to exercise rights: Access, Erasure, Objection impossible with 6 copies + model | High, no inventory, no DSR process, model cannot forget | High, subjects cannot get deletion, model keeps memory | High | F1-07, F1-06 |
| R7 | Breach notification failure, 72h impossible | High, no logging, no playbook, no DPO process | High, ICO fine, loss of trust, LuxeMart contract loss | High | F1-01 to F1-07 |
| R8 | Reputational harm, LuxeMart contract loss, ICO fine up to 4% global turnover, NDPC fine up to 2% + 10M NGN, per Audit Sec 8 | Medium, if breach becomes public | High, fines, audits, contract loss, hiring impact | High | All F1 |

**Overall BEFORE risk: Critical.** Processing must NOT continue in BEFORE form. Model must NOT be trained on raw emails.

---

## 8. Mitigations - AFTER controls

| Risk | AFTER control | Owner | Residual Likelihood | Residual Severity | Residual Risk |
| :--- | :--- | :--- | :--- | :--- | :--- |
| R1 Phishing | SFTP mTLS, S3 KMS eu-west-1, Lake Formation sales-group only, VPC Endpoint, SSO + MFA, no Dropbox, no WhatsApp, no Gmail, no GitHub public. Raw CSV deleted 365d. | Engineering + Sales | Low | High, phishing still possible if breach, but exposure is single copy, pseudonymised | Medium |
| R2 Profiling harm | Pseudonymise before training, minimise features, model governance, Art 22 assessment in DPA, human review path, log model version + hashed ID + features for each forecast, explainability via LuxeMart. | DPO + DS + LuxeMart | Low | High, profiling still has impact, even with pseudonymisation | Medium |
| R3 Model memorisation | Train only on hashed ID, no raw email in training, no model.pkl with raw emails, Model Registry hashed ID only, test for memorisation, delete model version on DSR, retrain without deleted IDs. | DS + Engineering | Low | Critical, model can still memorise hashed features if overtrained, needs testing | Medium |
| R4 Re-identification | Pseudonymise with salt, store salt in Secrets Manager KMS, separate mapping table S3 KMS + Lake Formation, no mapping in Aurora training table, no mapping in model, access to mapping is HR + DPO only, not DS. | Engineering + DPO | Low | High | Low |
| R5 Ex-staff / outsider | Identity Center SSO + MFA, revoke on exit, no WhatsApp, no Dropbox, no personal Gmail, no public GitHub, CloudTrail audit, GuardDuty + Macie. | Engineering | Low | High | Low |
| R6 Rights impossible | Single copy S3 + Aurora, DSR API POST /dsr with hashed ID, consent-ledger + dsr-requests, S3 Inventory + CloudTrail proof, Lifecycle auto-delete, model retraining on DSR, DSR log. | Engineering + DPO | Low | Medium, model retraining takes time, must be documented in DPA | Low |
| R7 Breach 72h | EventBridge to SNS to DPO + LuxeMart, Step Function playbook, forensics account, CloudTrail + Macie + GuardDuty, pre-filled 72h template for ICO + NDPC. Notify LuxeMart within 24h internal. | DPO + Engineering | Low | High | Medium |
| R8 Reputational / fine | DPO oversight, quarterly review, evidence in /evidence/, compliance-mapping, RoPA, DPA + TIA, model governance. | DPO | Low | High | Medium |

**Overall AFTER residual risk: Medium.** Acceptable only with DPO oversight, model governance, LuxeMart DPA + TIA, and quarterly review. Profiling is inherently high risk, even with pseudonymisation. Residual risk is NOT Low.

---

## 9. Pseudonymisation and DSR erasure - Technical detail

This section is specific to profiling DPIA. It must be implemented in Terraform + Lambda + Model Registry, not just in policy.

### 9.1 Pseudonymisation design

- **Input:** Raw CSV in S3, `s3://aftercast-prod-restricted-pii-eu-west-1/raw/`, KMS CMK, eu-west-1, Block Public, Versioning.
- **Lambda:** `lambda-pseudonymise`, triggered by S3 Event, validates, classifies, pseudonymises.
- **Hash:** Email to `sha256_hashed_id` = `SHA256(email_lower_trimmed + salt)`. Salt stored in Secrets Manager, KMS, not in code, not in model, not in Aurora training table.
- **Mapping:** Mapping table `email -> hashed_id` stored in separate S3 prefix `mapping/`, KMS, Lake Formation sales-group + DPO only, not DS. DS never sees raw email.
- **Training table:** Aurora `aftercast-prod-aurora-eu-west-1`, table `forecasting_features`, columns `hashed_id, purchase_features`, no name, no email, no phone, no address. `SELECT hashed_id, purchase_features` only.
- **Model:** Model Registry, `hashed_id` only, no raw email, no model.pkl with raw emails, versioned, with feature list, training date, and DSR retraining log.
- **Logs:** CloudTrail + S3 Inventory, no PII in logs, or pseudonymised hashed ID only. If a log contains raw email, it must be redacted before it is kept 6y.

### 9.2 DSR erasure via hashed ID

Right to Erasure, GDPR Art 17, NDPA Sec 36, is hard for AI models. Deleting a row does not delete model memory. AFTER must do:

1. **Request:** Data subject contacts LuxeMart, LuxeMart forwards DSR to AfterCast via DPA, with `hashed_id` or email to be hashed via same salt, to find `hashed_id`.
2. **Delete raw:** Delete raw CSV row from S3, delete mapping row from mapping table, delete Aurora row for `hashed_id`.
3. **Delete model version:** Mark model version that was trained on that `hashed_id` as tainted, do NOT use it for new forecasts.
4. **Retrain:** Retrain model without that `hashed_id`, create new model version, with retraining log, date, and DSR ID.
5. **Proof:** S3 Inventory + CloudTrail DeleteObject + Macie + Model Registry version log, kept 6y in Glacier Deep Archive, with PII NOT kept 6y, proof contains `hashed_id` + DSR ID only, not raw email.
6. **Tell LuxeMart:** Confirm erasure to LuxeMart within DPA SLA, so LuxeMart can confirm to data subject.

**What is NOT erasure:** Deleting the CSV but keeping the model is NOT erasure. Keeping `model.pkl` with raw emails is NOT erasure. Keeping the mapping table is NOT erasure.

### 9.3 Model governance

- Model versioning: Every model version has version, training date, feature list, hashed ID count, DSR retraining log, and approval by DPO + DS.
- Memorisation test: Before deployment, test model for memorisation of training data, for example membership inference test, and document result. If memorisation is high, retrain with regularisation, differential privacy, or fewer epochs, as documented in model card.
- Bias test: Before deployment, test for bias by region, gender, or other protected characteristic, if LuxeMart instructs it, and document result. If bias is found, do NOT deploy without LuxeMart approval and mitigation.
- Model card: Each model version has a model card, with purpose, features, limitations, Art 22 assessment, and DSR retraining log.

---

## 10. Action plan

| # | Action | Owner | Deadline | Evidence |
| :--- | :--- | :--- | :--- | :--- |
| A1 | Stop WeTransfer + Dropbox + WhatsApp + Gmail intake. No new LuxeMart CSV via BEFORE channels. | Funmi Daniels, Sales | Week 1, before AFTER launch | Intake block, Sales training log |
| A2 | Delete BEFORE copies: Dropbox, Gmail, WhatsApp, MacBook, local Postgres, GitHub public repo, with proof. Revoke WhatsApp ex-staff, revoke Dropbox link. | Funmi Daniels + Amara Eze + Engineering | Week 1 | Dropbox Trash empty, Gmail Trash empty, WhatsApp group leave, GitHub repo delete, MacBook wipe proof |
| A3 | Sign DPA with LuxeMart, with Art 28, Art 22 assessment, retention 365d, DSR SLA, breach 24h, sub-processor AWS eu-west-1, TIA. | Kenneth Pratt, DPO + Aisha Bello, CEO + LuxeMart | Month 1, before AFTER intake | Signed DPA, TIA, Art 22 assessment |
| A4 | Complete TIA for transfers, with LuxeMart, covering Nigeria law, eu-west-1, technical + organisational measures. | DPO + LuxeMart | Month 1 | TIA document |
| A5 | Build SFTP mTLS + S3 KMS eu-west-1 + Lambda pseudonymisation + Lake Formation sales-group only + VPC Endpoint + ECS Fargate STS 1h + Aurora hashed ID only. | Engineering | Month 1 | Terraform, S3 Inventory, Lambda logs, VPC Endpoint |
| A6 | Build Model Registry hashed ID only, no model.pkl with raw emails, model versioning, model card, memorisation test, bias test. | DS + Engineering | Month 1 | Model Registry, model card, test logs |
| A7 | Build DSR API POST /dsr with hashed ID, retraining workflow, deletion proof to Glacier Deep Archive 6y, PII NOT kept 6y. | Engineering + DPO | Month 2 | API test, DSR log, retraining log |
| A8 | Update LuxeMart privacy notice, with AfterCast as processor, profiling, retention, rights, transfers, DPA + TIA summary. | LuxeMart + DPO | Month 1 | LuxeMart privacy notice, with AfterCast text |
| A9 | Implement breach playbook, EventBridge to SNS to DPO + LuxeMart, Step Function, forensics account, 72h template for ICO + NDPC. | DPO + Engineering | Month 2 | Playbook test proof, EventBridge logs |
| A10 | Train Sales + DS on new process, no personal Gmail, no Dropbox, no WhatsApp, no SELECT *, no raw email in model. | DPO | Month 2 | Training log, attendance |
| A11 | Quarterly DPIA review, check residual risk, check model governance, check retention compliance, check access logs, check DSR retraining. | DPO | Quarterly, first review 18th Jan 2027 | Review minutes, CloudTrail audit, model card |

---

## 11. Compliance mapping, summary

Full texts in `compliance/compliance-mapping.md`, Appendix F.

| Requirement | GDPR | NDPA | BEFORE | AFTER |
| :--- | :--- | :--- | :--- | :--- |
| DPIA | Art 35 | Sec 44 | No DPIA | This DPIA, DPIA-LuxeMart-Profiling |
| Processor DPA | Art 28 | Sec 29 | No DPA | Signed DPA, Art 28, Art 22, DSR SLA |
| Transfers | Art 44 | Sec 41 | WeTransfer, Dropbox US, WhatsApp US, GitHub US, no SCCs | SFTP mTLS, eu-west-1 only, SCCs + TIA + DPA |
| Privacy Notice | Art 13 | Sec 27 | No notice, customers did not know AfterCast existed | LuxeMart notice updated, AfterCast as processor, profiling disclosed |
| Lawful Basis | Art 6 | Sec 25 | No documented basis | Contract via DPA, LuxeMart basis + Art 22 assessment |
| Automated Decisions | Art 22 | Sec 44 + Sec 27 | No Art 22 assessment | Art 22 assessment in DPA, human review path, explainability log |
| Erasure | Art 17 | Sec 36 | Impossible, 6 copies + model | DSR via hashed ID + retraining + proof |
| Security | Art 32 | Sec 39 | No MFA, no KMS, no VPC, public GitHub | KMS, TLS 1.2, SSO + MFA, VPC Endpoint, Lake Formation, STS 1h |
| Breach 72h | Art 33 | Sec 40 | No playbook | Playbook + SNS to DPO + LuxeMart + forensics, 24h internal to LuxeMart |
| By Design | Art 25 | Sec 24 + Sec 44 | No pseudonymisation, SELECT * | Pseudonymise before training, minimise features, model governance |
| RoPA | Art 30 | Sec 29 | No RoPA | RoPA, `compliance/RoPA.xlsx` |
| Retention | Art 5(1)(e) | Sec 24(d) | Infinite | Raw CSV 365d, logs proof 6y, PII NOT kept 6y |

---

## 12. Decision and sign-off

**DPO opinion:** High risk in BEFORE. AFTER controls reduce risk to Medium residual. Processing may proceed in AFTER form only, subject to Action Plan in Sec 10, DPA + TIA with LuxeMart, model governance, and quarterly review. Do NOT continue BEFORE processing. Do NOT train a model on raw emails.

**Controller decision, AfterCast AI as processor:** Approve AFTER processing, subject to Action Plan and DPA + TIA with LuxeMart. Reject BEFORE processing.

**Note:** LuxeMart UK as controller must also approve its own controller DPIA and DPA. AfterCast approval does not replace LuxeMart approval.

| Role | Name | Date | Signature |
| :--- | :--- | :--- | :--- |
| DPO | Kenneth Pratt, Legal / Compliance | 18th October 2026 |  |
| Owner, Sales | Funmi Daniels, Sales AE | 18th October 2026 |  |
| Owner, Data Science | Amara Eze, Data Science | 18th October 2026 |  |
| Approver, CEO | Aisha Bello, CEO | 18th October 2026 |  |

**Next review:** 18th April 2027, or earlier if model changes, new data source, new purpose, breach occurs, or LuxeMart changes instructions.

**Related DPIA:** DPIA-HR-2025-01, `compliance/DPIA-HR-2025-01.md`, for Flow 2 HR only. This DPIA covers Flow 1 only. This DPIA does not cover Flow 2 HR.

