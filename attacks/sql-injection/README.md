Phase 2 — SQL Injection with SQLMap



Objective



Exploit the SQL injection vulnerability in DVWA's SQLi module to extract user credentials from the underlying database, and verify that Wazuh detects the attack through Apache access log monitoring.



Environment



Attacker: Kali Linux (192.168.1.22)

Target: Windows 11 + DVWA (192.168.1.17)

Target URL: http://192.168.1.17/DVWA/vulnerabilities/sqli/

Tool: SQLMap v1.9.11

Session: PHPSESSID obtained by logging into DVWA from Kali



Pre-requisites



DVWA security level set to Low.

Wazuh agent on the Windows host monitoring C:\\xampp\\apache\\logs\\access.log.

A valid DVWA session cookie (PHPSESSID) captured from an authenticated browser session on the attacker host.



Methodology



Step 1. Capture the session cookie



The SQLi endpoint requires authentication. Rather than logging in from the Windows host, the attacker logged into DVWA from Kali's Firefox and extracted the PHPSESSID cookie via DevTools. This mirrors how an external attacker would behave after obtaining valid credentials.



Step 2. Confirm the injection point



The first SQLMap run tested the id parameter for injection using multiple techniques. SQLMap confirmed four distinct injection types: boolean-based blind, error-based, time-based blind, and UNION query.



refer to images/sqlmap\_databases.png



The available databases were enumerated: dvwa, information\_schema, mysql, performance\_schema, phpmyadmin, and test. The dvwa database is the primary target. The rest are MySQL internal or default databases.



Step 3. Enumerate tables in dvwa



With the injection confirmed, SQLMap was pointed at the dvwa database to list its tables.



refer to images/sqlmap\_tables.png



Four tables were found: access\_log, guestbook, security\_log, and users. The users table is where DVWA stores credentials.



Step 4. Extract credentials



The final command dumped the user and password columns from users. SQLMap recognized the passwords as MD5 hashes and ran a dictionary-based cracking attack, recovering every hash within seconds.



refer to images/sqlmap\_dump\_credentials.png



Recovered credentials:



admin, password

gordonb, abc123

1337, charley

pablo, letmein

smithy, password



All five MD5 hashes were cracked. The full table was also exported to CSV by SQLMap at /home/katyusha303/.local/share/sqlmap/output/192.168.1.17/dump/dvwa/users.csv.



Detection



Wazuh picked up the attack in real time via Apache log inspection. Rule 31106, "A web attack returned code 200 (success)," fired four times during the SQLMap runs.



refer to images/wazuh\_alerts\_list.png



Expanding the alert shows the full HTTP request as seen by Apache, including the SQLMap payload embedded in the id parameter. This is the raw evidence of the injection reaching the server.



refer to images/wazuh\_alert\_details.png



Alert metadata:



Rule ID: 31106

Rule level: 6

Rule group: web, accesslog, attack

MITRE technique: T1190 (Exploit Public-Facing Application)

MITRE tactic: Initial Access

Decoder: web-accesslog

Log source: C:\\xampp\\apache\\logs\\access.log

Source IP: 192.168.1.25



Note on detection granularity



The specific rule 31103 (SQL injection attempt) did not fire for these payloads, even though they clearly contained SQL syntax. Only the broader rule 31106 (web attack returned HTTP 200) triggered. This is a known limitation of the default Wazuh ruleset, which depends on the specific patterns the built-in regex checks for. A custom rule covering SQLMap's UNION SELECT and error-based payloads would provide higher fidelity detection. This is left as future work.



Indicators of Compromise



Attacker host: 192.168.1.22 (Kali Linux)

Target endpoint: GET /DVWA/vulnerabilities/sqli/?id=...

Target host: 192.168.1.17 (Windows 11 + XAMPP + DVWA)

User-Agent: sqlmap/1.9.11#stable

Injected payloads observed: UNION SELECT, error-based FLOOR payloads, time-based SLEEP payloads

Wazuh rule triggered: 31106 (level 6)

Log artifact: C:\\xampp\\apache\\logs\\access.log



Analysis



The SQLi attack was entirely successful. SQLMap identified four independent injection techniques and was able to enumerate databases, tables, and finally extract and crack every credential in the users table.



The credentials retrieved are the most significant outcome. All five passwords were cracked using a default dictionary in under five seconds. MD5 is unsalted and trivially crackable. In a production scenario this would represent a full compromise of every account in the application.



Detection was achieved but with lower fidelity than ideal. The default ruleset catches the attack as a generic web attack rather than a named SQL injection event. This shows the value of layering custom rules on top of the built-in ones. The base ruleset catches broad categories; custom rules catch specific behaviors.



Lessons Learned



SQLMap's initial detection phase is worth watching closely. Seeing the four injection types reported is more informative than jumping straight to the dump command.



Unsalted MD5 hashes should be treated as equivalent to plaintext. Any modern password storage scheme such as bcrypt, argon2, or scrypt would make this attack dramatically harder.



The gap between rule 31103 and rule 31106 in this case underlines why detection engineering is iterative. The built-in rules are a starting point, not a finished product.



Next Steps



Write a custom Wazuh rule (ID 100000+) to detect SQLMap's specific payload patterns, such as UNION SELECT with CONCAT or error-based FLOOR payloads.



Run the same SQLi attack against DVWA at Medium and High security levels to observe how detection changes.



Document the CSV dump as supplementary evidence in the evidence folder.

