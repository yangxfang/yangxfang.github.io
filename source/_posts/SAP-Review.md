---
title: What can be reviewed in a SAP S/4 Hana project
copyright: true
top: 55
date: 2019-03-01 11:34:03
tags:
- SAP S/4 Hana
- internal audit
---

*-- to be updated --*



This is a high-level topic list (non-exhaustive) for an IT review about SAP S/4 Hana project deployment. For items very straight-forward, I won't further explain.

## Deployment

1. Migration impact analysis

   *It should be the basis for well executing following topics under deployment.*

2. Testing: ABAP code check, unit test, functional test, regression test

   *From functioning, connectivity, performance, security perspectives.*

3. Backup/rollback plan for both business and IT sides

4. De-complexity of IT landscape (SAP and surrounding systems, custom transactions and jobs, etc.)

5. Integration with other platforms

6. Decommissioning of legacy systems

7. Transition into operations 

## Business transformation

1. Process controls
2. User experience (SAP FIORI)

## Security and Compliance

1. Authentication & user management
2. Blueprint design (authorization & segregation of duties)
3. Network & communication layer security
4. Security hardening (by leveraging health check capabilities of SAP S/4 Hana)
5. Data encryption (prod, backup, archived data)
6. Web/App security (Threat detection & prevention, Admins, Space, Network configuration, secure code)
7. File system (anti-virus/anti-malware, sharing)
8. Data retention management (across database, file system, storage) 
9. Privacy (if applicable): logging, masking, anonymization
10. Auditing capability (logging, monitoring)

## Business Continuity

1. Backup, archiving, restoration
2. High availability
3. Disaster recovery

## Operating model

1. Change & release management
2. QA & testing automation
3. User management
4. Continuous security hardening & monitoring
5. (Tenant) database management
6. Performance management: monitor, incident, problem, crisis
7. License & cost optimization
8. Key risks, internal controls assessment & remediation 