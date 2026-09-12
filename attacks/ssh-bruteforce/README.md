HTTP Brute Force with Hydra:



Objective:



Simulate a credential brute-force attack against the DVWA login form from

an attacker-controlled host, and verify that Wazuh detects the attack through

Apache access log monitoring.



Environment Setup:



Attacker:  Kali Linux — 192.168.1.17

Target : Windows 11 + DVWA — 192.168.1.27

Target URL : http://192.168.1.27/DVWA/login.php

Tool : Hydra v9.6 (`http-post-form module)

Wordlist : /usr/share/wordlists/rockyou.txt



Pre-requisites:



\- DVWA security level set to Low (no rate-limiting or lockout)

\- Wazuh agent on the Windows host configured to monitor

&#x20; `C:\\\\xampp\\\\apache\\\\logs\\\\access.log` and `error.log`

\- Apache and MySQL services running via XAMPP



Methodology:



Hydra was pointed at the DVWA login endpoint using its `http-post-form`

module. The form fields and failure condition were declared in the module

string:



\- Form path: /DVWA/login.php

\- POST body:\*\* `username=^USER^\\\&password=^PASS^\\\&Login=Login`

\- Failure indicator: redirect to login.php (HTTP 302)



see images/hydra\_bruteforce\_attack.png



Wazuh-Alert:

As the attack started from the kali VM, the wazuh dashboard showed an alert

reporting "a HIGH amount of post requests in a short period of time" which

was followed by rule ID 31533.

see images/bruteforce\_alert01.png



When checking the details of the alert, The detection

did not come from a brute-force-specific rule.

Wazuh instead correlated the request rate at the web server and flagged the burst as

bot-like behavior. This is a realistic outcome: many SOCs catch credential

attacks indirectly through rate-based rules because the underlying

authentication events (in this case, DVWA's own login tracking) are not

forwarded to the SIEM.

The expanded alert records data.srcip as ::1 (IPv6 localhost) because the

captured log line originated from a local request. In a full production deployment,

the source IP field would show the attacker's IP (192.168.1.17) for external requests.

This is a limitation of testing against a local Apache instance.



see images/bruteforce\_alert02.png

see images/bruteforce\_alert03.png

