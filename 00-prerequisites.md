# **Prerequisites**

**File:** `00-prerequisites.md`  
**Repo:** AfterCastAI-Greenfield-Cloud-Migration-and-Compliance/  
**Read this first.** Everything later must match this file \+ Audit Report 5\.

If a later file disagrees with this file, fix the later file. Do not invent new names, counts, or locations.

---

## **1\. Background**

AfterCast AI is a small company with 30 staff in Yaba, Lagos and London. To get work done fast, staff used whatever tools were at hand: personal Gmail, personal Dropbox, WhatsApp, Google Sheets, a QNAP in the office, and USB drives.

That created a scattered system. When data lives in many places, there are many ways for it to leak. There is also no single place to give or remove access, check logs, or restore a backup. If one link is shared, one laptop is lost, or one USB is stolen, sensitive data is exposed.

A unified, access-controlled cloud fixes this. All data lives in one controlled place. Access is based on job role and can be removed quickly. All activity is logged in one place. Staff can work securely from anywhere, and services stay available even if one office or device fails. Security is built in from the start, not added later. Compliance with GDPR and NDPA 2023 gives a clear baseline for that security, protects staff and customers, and avoids fines.

---

## **2\. Checklist \- What you need before Phase 1**

* This file read and understood  
* Audit Report 5 read, `docs/0.2-security-audit-report.md`  
* Phase 0.5 docs done: RoPA, DPIA, Classification \+ Retention, Business Case  
* Personal Gmail with plus-addressing, see Sec 4  
* AWS account access, 5 accounts via plus-addressing  
* Terraform \+ GitHub setup  
* No domain purchase, no Google Workspace signup needed

---

## **3\. Company snapshot**

| Item | Fact |
| ----- | ----- |
| Company | AfterCast AI, fictional |
| Age | 3.5 years old |
| Size | 30 staff, Yaba Lagos \+ London remote |
| Product | AI-powered demand forecasting for retail brands. What will they buy next? |
| Start state | Greenfield cloud introduction. No AWS, no Azure, no public cloud. On-prem \+ shadow SaaS. |
| Domain | `aftercast.ai`, fictional, not purchased |
| Email format | `firstname.lastname@aftercast.ai` |
| Compliance | Data Controller of Major Importance, NDPA 2023\. AI profiling, \>10k subjects. Requires DPO, DPIA, NDPC filing. |
| DPO | Kenneth Pratt, Legal / Compliance. Reports to CEO Aisha Bello, not Engineering. GDPR Art 37, NDPA Sec 32\. |

**Clients:**

* **Client 1:** LuxeMart UK, London, GDPR. In scope, Flow 1\.  
* **Client 2:** ShopRite Naija, Lagos, NDPA. Future client only. NOT in Phase 0 scope. No flow, no count.

Do not attribute Flow 2 BVN leak to ShopRite. Do not claim Series A closed unless added to audit background.

---

## **4\. Accounts and identity**

### **AWS accounts, via Gmail plus-addressing**

Do NOT create separate Gmail accounts. Use one personal Gmail with plus-addressing.

| Email | Account |
| ----- | ----- |
| `yourname+aftercast-root@gmail.com` | Management |
| `yourname+aftercast-logarchive@gmail.com` | Log Archive |
| `yourname+aftercast-audit@gmail.com` | Audit |
| `yourname+aftercast-prod@gmail.com` | Prod Workloads |
| `yourname+aftercast-dev@gmail.com` | Dev Workloads |

### **Identity**

* **IAM Identity Center, Internal Directory.** Simulated Google Workspace.  
* No Google Workspace signup. No domain purchase.  
* No IAM Users for humans. SSO \+ MFA only.  
* Groups: `sales-group`, `hr-group`, `ds-group`, `finance-group`, plus `DPO-Audit`. Data Science anonymized view: `DataScience-AnonymizedOnly` subgroup.

---

## **5\. People**

Full staff list, canonical names. Use these exact names everywhere. Auditor will Ctrl+F.

| Dept | Headcount | Staff |
| ----- | ----- | ----- |
| Executive | 3 | Aisha Bello (CEO), David Chen (CTO, Co-founder), Tolu Adeyemi (COO) |
| Engineering | 8 | Emeka Nwosu (Lead Backend), Priya Singh (Backend), James Okoro (Frontend), Fatima Yusuf (Frontend), Chidi Obi (Mobile), You (Cloud Security Engineer), Sarah Johnson (QA), Ibrahim Musa (Intern) |
| Product \+ Design | 3 | Zainab Ali (PM), Leo Martins (Designer), Grace Okafor (UX Researcher) |
| Data Science | 4 | Dr. Kenji Tanaka (Lead), Amara Eze (ML Engineer), Samuel Kola (Data Engineer), Nneka Udo (Data Analyst) |
| Sales | 3 | Marcus Cole (Lead), Funmi Daniels (Sales AE), Ahmed Sule (SDR) |
| Marketing | 3 | Chloe Williams (Lead), Bayo Fashola (Content), Hauwa Ibrahim (Growth) |
| Customer Success / Ops | 3 | Lydia Mensah (CS Lead), Peter Dada (Support), Joy Alabi (Ops Manager) |
| Finance / Admin / HR | 4 | Bola Tinubu (Finance Manager), Ruth Adamu (Admin Officer), Kenneth Pratt (Legal \+ DPO), Ifeoma Nnaji (People \+ Culture) |

**Naming rules:**

* Funmi Daniels is always Sales AE, never Customer Success.  
* Ifeoma Nnaji is always People \+ Culture, P\&C/HR.  
* Bola Tinubu is always Finance.  
* Amara Eze is always Data Science.

**Email formats, em-dash separator:**

In final docs, use em-dash as separator, for example:

```
Personal Gmail: funmi.personal@gmail.com — Funmi Daniels (Sales, AE)
WorkMail: funmi.daniels@aftercast.ai — Funmi Daniels (Sales, AE)
Personal Gmail: ifeoma.personal@gmail.com — Ifeoma Nnaji (P&C/HR)
WorkMail: ifeoma.nnaji@aftercast.ai — Ifeoma Nnaji (P&C/HR)
Personal Gmail: bola.personal@gmail.com — Bola Tinubu (Finance)
WorkMail: bola.tinubu@aftercast.ai — Bola Tinubu (Finance)
```

All personal Gmail addresses are fictional. See disclaimer in README.

---

## **6\. Data in scope**

### **Flow 1 \- LuxeMart UK, 12k EU PII**

| Item | Fact |
| ----- | ----- |
| Source | LuxeMart UK |
| Data | Name, Email, Phone, Delivery Address, Loyalty ID, Purchase History |
| Count | 12,000 |
| BEFORE copies | 6\. Dropbox, WhatsApp, Gmail, MacBook, GitHub, plus local DB |
| BEFORE exposure | 72,000 points (12k x 6\) |
| AFTER copies | 1\. S3 `s3://aftercast-prod-restricted-pii-eu-west-1/` |
| AFTER retention | 365 days, then auto-delete |

### **Flow 2 \- HR Applicants, 143 NG Sensitive**

| Item | Fact |
| ----- | ----- |
| Source | Job Applicants, 143 |
| Data | NIN, BVN, Passport, Bank Details, CV, Home Address |
| Count | 143 total \= 11 hired \+ 132 rejected |
| BEFORE copies | 7\. Sheet, Gmail Sent \+ Inbox, Dell, QNAP, USB, plus Trash |
| BEFORE exposure | 1,001 points (143 x 7\) |
| AFTER copies | 1\. S3 `s3://aftercast-hr-restricted-pii-eu-west-1/` |
| AFTER retention | Rejected 180 days, Hired 6 years |

Total BEFORE copies, both flows: 13\.

**Health info is NOT in scope.** Do not add it.  
**IBAN is NOT in scope.** Use NIN / BVN, not IBAN.

---

## **7\. Technical baseline**

| Item | BEFORE | AFTER |
| ----- | ----- | ----- |
| Cloud | None, on-prem \+ shadow SaaS | AWS only |
| Stack | Python / FastAPI, React, Postgres | ECS Fargate \+ Aurora Postgres |
| Network, on-prem | `192.168.1.0/24` | — |
| Network, AWS | — | `10.0.0.0/8`, Hub `10.10.0.0/16` |
| QNAP | `192.168.1.10`, admin/admin, SMBv1, Yaba | Decommissioned, no QNAP, no USB, no SMBv1 |
| Residency | US servers, Dropbox US, Google US | `eu-west-1` primary, `eu-west-2` backup / DR only. No US for PII. |
| Buckets | Personal Dropbox, personal Drive | `s3://aftercast-prod-restricted-pii-eu-west-1/`, `s3://aftercast-hr-restricted-pii-eu-west-1/`, KMS CMK, Block Public, Bucket Policy Deny not `eu-west-1/2` |
| Databases | Local Postgres on MacBook, Sheet | `aftercast-prod-aurora-eu-west-1`, `aftercast-prod-aurora-hr-eu-west-1`, column KMS |
| Ingestion | WeTransfer, WhatsApp, personal Gmail | SFTP mTLS \+ TLS 1.2, VPC Endpoint S3 PrivateLink, pre-signed URL 1h via WorkMail |
| Model training | Email as `user_id`, raw email in features | Hashed ID only, `sha256_hashed_id`, Lake Formation anonymized view |

---

## **8\. Retention and classification**

| Data | Retention | Notes |
| ----- | ----- | ----- |
| PII, Flow 1 | 365 days | Then auto-delete raw CSV, audit log to Glacier Deep Archive 6y |
| Purchase History | 2 years | — |
| Employee Data, hired | 6 years | Flow 2 hired: 6y, then Deep Archive |
| Applicant Data, rejected | 180 days | Flow 2 rejected 132: Glacier 30d, Delete 180d |
| Logs | 1y hot \+ 6y cold | Logs themselves are personal data, hash email to `sha256_hashed_id` before archiving |

**DataClass tags, enforce in AWS:**

* `Public`, `Internal`, `Confidential`, `Restricted-PII`  
* `Restricted-PII+Sensitive` for NIN, BVN. Do not use SPII.

**Other tags:**

* `DataSubject`: `[EU]`, `[NG]`, `[Employee]`  
* `Purpose`: `[model-training]`, `[marketing]`, `[hr]`  
* `RetentionDays`: `[90]`, `[180]`, `[365]`, `[1825]`

Pseudonymization is a technique, not a RetentionDays value. Do not list it as RetentionDays.

---

## **9\. Compliance baseline**

| Law | Key articles / sections |
| ----- | ----- |
| GDPR | Art 5(1)(a)-(f), Art 25 By Design, Art 28 Processor DPA, Art 32 Security, Art 33 Breach 72h, Art 35 DPIA, Art 44 Transfers |
| NDPA 2023 | Sec 24 Principles, Sec 25 Lawful Basis, Sec 27 Privacy Notice, Sec 29 RoPA, Sec 32 DPO, Sec 35 Withdrawal, Sec 36 Access, Sec 39 Confidentiality, Sec 40 Breach 72h, Sec 41 Cross-Border, Sec 44 DPIA |

**Lawful bases:**

* HR: Contract, plus explicit consent for Sensitive per Sec 35  
* Marketing: Contract via DPA, not Legitimate Interest \+ Consent alone. LuxeMart DPA required.  
* Product: Contract

**Findings:** 14 total \= 7 Critical \+ 7 High, No Medium. F1-01 to F1-07, F2-01 to F2-07. Full register in Audit Report.

Full texts in `compliance/compliance-mapping.md`, Appendix F.

---

## **10\. Roadmap \- 11 Phases, 45 Labs**

Counted labs are 45\. Do not claim 58 until additional labs are added.

| Phase | Name | Status |
| ----- | ----- | ----- |
| 0 | Discovery \+ Privacy Foundation | Complete. Audit Report 5\. Zero-Cloud Retrospective Audit, manual-first. No Prowler in Phase 0, no cloud to scan. |
| 0.5 | Compliance Artifacts | Next. RoPA, DPIA, Classification \+ Retention, Business Case. Before Terraform. |
| 1 | Organization, Identity \+ Governance | Next after 0.5. Orgs \+ SCPs, Identity Center, Control Tower, Log Archive, CloudTrail, GuardDuty, Config. |
| 2 | Network \+ Data Residency | KMS, Lake Formation, S3 Restricted PII, Macie, WorkMail \+ DLP. |
| 3 | VPC Hub \+ Compute | VPC Hub, VPC Endpoint, ECS Fargate \+ Aurora, API Gateway \+ Cognito \+ WAF. |
| 4 | Ingestion \+ Migration | SFTP mTLS \+ Lambda pseudonymization, migration, cutover, deletion of BEFORE copies. |
| 5 | Operations \+ Evidence | Security Lake, EventBridge to SNS to DPO, 72h breach playbook, DSR automation, Before vs After evidence. |
| 6 | IAM PBAC | ABAC \+ PBAC. Fix OR precedence: use parentheses. |
| 7 | Automation as Compliance | Terraform backend, Policy-as-Code, Golden AMI. |
| 8 | Detection \+ Response | SIEM, Alerting, Breach Playbook, Forensics. Use generic ticket \+ SNS to DPO, not Jira. |
| 9 | DSR \+ Lifecycle | DSR API, Retention enforcement, WAF NIN / BVN patterns, Backup \+ DR. |
| 10 | Final Audit \+ Evidence | Dashboard, Before vs After Report. Prowler numbers to be measured in Phase 3, do NOT claim 87% vs 5%. |

**Phase 0.5 in order:**

1. RoPA, `compliance/RoPA.xlsx`, GDPR Art 30, NDPA Sec 29  
2. DPIA, `compliance/DPIA-HR-2025-01.md` \+ `compliance/DPIA-LuxeMart-Profiling.md`, GDPR Art 35, NDPA Sec 44  
3. Classification \+ Retention, `compliance/data-classification-policy.md` \+ `compliance/retention-policy.md`  
4. Business Case, `docs/0.6-cloud-business-case.md`

**Repo structure:**

```
/compliance/ - RoPA, DPIA, policies, mapping
/docs/ - Audit, Business Case, Glossary, Appendixes
/terraform/ - Bootstrap, phases
/evidence/ - Audit BEFORE proof, labs AFTER proof
```

---

## **11\. Rules for all later files**

1. **1 Fact, 1 Name.** Same character, location, count, DataClass everywhere. Ctrl+F must match.  
2. **No real PII.** Only `john.doe@example.com` and simulated addresses.  
3. **Disclaimer.** Fictional Gmail addresses need disclaimer in every doc that uses them.  
4. **Residency.** No US for PII. Primary `eu-west-1`, backup `eu-west-2` only.  
5. **No PCI-DSS.** No card data in scope. Do not add it.  
6. **No Jira.** Use generic ticket \+ SNS to DPO.  
7. **No Google Workspace.** Use Identity Center Internal Directory.  
8. **No Highly-Restricted-PII as OU.** That is a DataClass tag, not an OU.

