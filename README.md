# Mediroza General Hospital – Black-Box Penetration Testing

## Project Overview

This project documents a five-day black-box penetration-testing assessment conducted against the Mediroza General Hospital website as part of the Networkwalks Cybersecurity Internship (Batch B083, Week 4).

**Target:** `https://medirozahospital.com`

**Assessment Type:** Black-Box Web Application Penetration Testing

**Duration:** 5 Days

**Operating Environment:** Kali Linux

## Objectives

- Identify publicly exposed resources and web application entry points.
- Assess the patient authentication mechanism.
- Identify and demonstrate security vulnerabilities.
- Retrieve three designated laboratory reports.
- Analyse PDF encryption and recover document passwords.
- Investigate exposed database information.
- Document security findings, business impact and remediation.

## Scope

Testing was restricted to the authorised target domain and the activities specified in the project brief.

Excluded activities:
- Social engineering.
- Denial-of-service testing.
- Testing unrelated systems or infrastructure.

## Tools Used

- Kali Linux
- curl
- WhatWeb
- pdfinfo
- pdftotext
- Linux command-line utilities

---

# Milestone 1: Initial Access

## Objective

Identify weaknesses in the website and obtain access to the three designated patient laboratory reports.

### Activities Performed

**1. Reconnaissance**

- Identified the target's web server technology.
- Inspected public website pages.
- Identified patient and staff login portals.
- Examined `robots.txt` and the sitemap.

**2. Directory Enumeration**

Discovered directory listing enabled on:

- `/old/`
- `/patient/`

The `/old/` directory exposed an internal SQL database backup:

`mediroza_db_backup_2019.sql`

The patient directory exposed application resources, including login, download and portal scripts.

**3. Authentication Testing**

Tested the patient login mechanism for SQL injection.

The following username payload was used:

`admin' --`

The payload successfully bypassed the patient login during the assessment and provided access to the patient portal.

**4. Report Retrieval**

Retrieved the three designated laboratory reports:

- `patient_report_1.pdf`
- `patient_report_2.pdf`
- `patient_report_3.pdf`

Also retrieved the publicly accessible SQL database backup for further investigation.

### Findings

- SQL injection in patient authentication.
- Public directory listing.
- Publicly accessible database backup.
- Exposure of application directory structure.

### Evidence

---

# Milestone 2: Data Extraction and Password Recovery

## Objective

Analyse the three retrieved PDF reports, recover their passwords and extract their contents.

### Activities Performed

**1. PDF Encryption Inspection**

Used `pdfinfo` to inspect the properties of the three reports.

All three documents used:

- PDF version 1.4.
- RC4 encryption.
- A4 page size.
- One page per document.

**2. Password Recovery**

Successfully recovered the passwords protecting all three reports.

**3. Text Extraction**

Used `pdftotext` to extract the report contents after password recovery.

Commands used:

```bash
pdftotext -upw '123456' patient_report_1.pdf report1.txt

pdftotext -upw 'password' patient_report_2.pdf report2.txt

pdftotext -upw '!@#$%^&' patient_report_3.pdf report3.txt
```

### Metadata Observations

The reports shared the same PDF version, encryption algorithm, creator and producer.

Report 3 differed in two metadata properties:

- Author field: `j.malik`
- Custom Metadata: Yes

This difference was recorded for further investigation.

### Findings

- All three PDF passwords were recovered.
- All three reports were successfully decrypted and their text extracted.
- RC4 encryption was identified across the documents.
- Report 3 contained different metadata properties.

### Evidence

---

# Milestone 3: Data Exposure and Analysis

## Objective

Analyse the retrieved information and identify further sensitive data exposed by the target server.

### 1. Internal Database Backup

The publicly accessible SQL backup identified the database as:

`mediroza_hr`

It contained two tables:

| Table | Records | Information |
|---|---:|---|
| staff | 30 | Employee information and monthly salaries |
| shareholders | 10 | Shareholder ownership and share classes |

### 2. Employee Salary Analysis

Analysis of the 30 employee records produced the following results:

| Metric | Result |
|---|---:|
| Employee records | 30 |
| Total monthly payroll | R2,027,000 |
| Average monthly salary | R67,566.67 |
| Median monthly salary | R45,000 |
| Lowest monthly salary | R19,000 |
| Highest monthly salary | R160,000 |

Currency: South African Rand (ZAR).

### 3. Shareholder Analysis

| Metric | Result |
|---|---:|
| Shareholder records | 10 |
| Total ownership | 100% |
| Total shares | 1,000,000 |
| Largest individual holding | 18% |
| Smallest individual holding | 4% |
| Share classes | Ordinary and Preferential |

### 4. Additional Observation

The SQL backup header indicated a generation date of 27 August 2019, while some employee joining dates extended into 2020.

This inconsistency was recorded for further verification.

### Security Impact

The exposed backup contained confidential employee and business information, including salary records, personal identifiers and shareholder details.

Potential consequences include:

- Employee privacy violations.
- Exposure of confidential salary information.
- Disclosure of company ownership information.
- Increased risk of targeted phishing and impersonation.
- Reputational and regulatory consequences.

### Findings

The assessment confirmed that sensitive internal HR and shareholder information was accessible through a publicly exposed database backup.

---

# Milestone 4: Final Penetration Testing Report

## Findings Summary

| ID | Finding | Severity |
|---|---|---|
| F-01 | SQL Injection in Patient Authentication | Critical |
| F-02 | Publicly Accessible Internal Database Backup | Critical |
| F-03 | Public Directory Listing on Sensitive Directories | High |
| F-04 | Weak Password Protection on Patient PDF Reports | High |
| F-05 | Exposure of Sensitive Patient Reports Through Portal Access | High |

Severity ratings represent the assessed risk and are not formal CVSS scores.

## Recommended Remediation

### SQL Injection
- Use parameterised SQL queries.
- Implement prepared statements.
- Validate user input.
- Apply least-privilege database permissions.

### Database Backup Exposure
- Remove database backups from public directories.
- Store backups outside the web root.
- Restrict backup access.
- Review historical backups for sensitive information.

### Directory Listing
- Disable directory indexing.
- Restrict access to sensitive directories.
- Store application logs and backups outside publicly accessible paths.

### PDF Protection
- Replace weak password protection with modern encryption.
- Use strong, randomly generated passwords where appropriate.
- Deliver confidential reports through authenticated portals.
- Review document metadata before distribution.

### Patient Portal Security
- Enforce authentication and authorisation checks.
- Ensure users can access only their authorised records.
- Implement secure session management.
- Monitor suspicious report access and downloads.

## Overall Conclusion

The assessment identified multiple security weaknesses affecting the confidentiality of patient, employee and business information.

The most significant findings were SQL injection in the patient authentication mechanism and the public exposure of an internal HR database backup.

The successful retrieval of designated patient reports and the discovery of employee salary and shareholder information demonstrated the potential impact of these weaknesses.

The hospital should prioritise remediation of the identified vulnerabilities and conduct a follow-up assessment to verify that the weaknesses have been resolved.

---

## Final Penetration Testing Report

The full report documents the five-day black-box penetration test conducted against Mediroza General Hospital as part of the Networkwalks Cybersecurity Internship (Batch B083, Week 4).

It covers the assessment methodology, all four project milestones, identified vulnerabilities, evidence of exploitation, security impact, risk ratings and recommended remediation measures.

**Report:** [View MEDIROZA GENERAL HOSPITAL – BLACK-BOX PENETRATION TESTING REPORT (PDF)](docs/MEDIROZA%20GENERAL%20HOSPITAL_BLACK-BOX%20PENETRATION%20TESTING%20REPORT.pdf)

For the practical activities and detailed findings, see the [project documentation](Mediroza_Black_Box_Penetration_Testing.md).

---

## Ethical and Data Handling Considerations

This project involved authorised testing within the scope provided by the internship instructor.

Patient records, employee personal identifiers, contact details and recovered passwords have been excluded from this public documentation.

Original evidence should be stored securely and shared only with authorised project personnel.

**Project:** Networkwalks Cybersecurity Internship – Batch B083, Week 4

**Assessment:** Mediroza General Hospital Black-Box Penetration Testing
