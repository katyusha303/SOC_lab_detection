\# SOC Detection Lab



A home Security Operations Center lab built to practice detection, 

analysis, and incident reporting using Wazuh SIEM.



\## Overview



This project documents the design, deployment, and operation of a small 

SOC environment where attacks are simulated against a controlled target, 

detected by Wazuh, and written up as professional incident reports.



The objective was hands-on experience with the full detection pipeline — 

from log ingestion to alert triage to remediation planning — rather than 

theory or tutorial replication.



\## Architecture



Three hosts on the same subnet:



| Role | Host | IP Address |

|------|------|------------|

| Attacker | Kali Linux (VirtualBox) | 192.168.1.17 |

| Target | Windows 11 + DVWA (XAMPP) | 192.168.1.27 |

| SIEM | Wazuh Manager + Dashboard | 192.168.1.16 |



\*\*Data flow:\*\*

1\. Kali launches attacks against DVWA (running on Apache/MySQL/PHP via XAMPP).

2\. Apache writes requests to `access.log`; Windows writes auth events to the Security Event Log.

3\. The Wazuh agent on Windows forwards these logs to the Wazuh Manager.

4\. Detection rules on the manager generate alerts visible in the dashboard.



\## Project Scope



\*\*Attacks simulated:\*\*

\- SQL Injection against DVWA (sqlmap and manual payloads)

\- SSH Brute Force against the Windows host (Hydra)



\*\*Detection coverage:\*\*

\- Web attacks via Apache log inspection (rules 31100–31106)

\- Authentication attacks via Windows Event Log (rules 5710, 5711)

\- Custom correlation rule (ID 100000+) for repeated failed logins



\## Current Status



| Phase | Status |

|-------|--------|

| Wazuh stack deployment (VM, agent, dashboard) | Complete |

| DVWA deployment on XAMPP | Complete |

| Apache log ingestion and verification | Complete |

| SQL injection detection (verified) | Complete |

| SSH brute force simulation | In progress |

| Custom Wazuh rule (100000+) | In progress |

| Incident report | In progress |



\## Detections Achieved



| Rule ID | Description | Trigger |

|---------|-------------|---------|

| 31103 | SQL injection attempt | `UNION SELECT` payload in HTTP request |

| 5711 | SSHD brute force | Repeated authentication failures |

| 100001 | Custom: 3 failed logins in 60 seconds | Same source IP |



\## Repository Structure



\- `01-lab-setup/` — Deployment documentation for each component

\- `02-attacks/` — Attack methodology, commands, and payloads

\- `03-detection/` — Rule analysis and custom Wazuh rules

\- `04-troubleshooting/` — Documented issues and resolutions

\- `05-incident-report/` — Final incident report with timeline and IoCs

\- `06-future-upgrades/` — Planned enhancements



\## Troubleshooting Notes



The `04-troubleshooting/` directory documents every issue encountered 

during deployment — port conflicts, permission errors, agent enrollment 

failures, and firewall misconfigurations. Each entry follows a 

Symptom → Diagnosis → Root Cause → Resolution format.



These notes are retained because they represent the practical challenges 

of SIEM deployment and the diagnostic process required to resolve them.



\## Roadmap



\- MITRE ATT\&CK mapping for each detection rule

\- Threat intelligence enrichment (VirusTotal / AbuseIPDB integration)

\- Active Response configuration (automated IP blocking)

\- Multi-endpoint correlation (agent on Kali and additional Linux host)



\## Tools and Technologies



Wazuh · Kali Linux · Hydra · sqlmap · DVWA · XAMPP · Apache · MySQL · 

VirtualBox · Windows 11



\## Deliverables



\- Incident report (see `05-incident-report/`)

\- Custom Wazuh detection rules (see `03-detection/custom-rules/`)



\---



\*Status: Active development. Last updated: September 2026.\*

