Kali Linux is an open source OS based on Linux that is specifically used for cybersecurity in the domains of pentesting, ethical hacking, etc. It is the most popular offensive security OS that turns out to be very helpful when testing apps and services we own against well-known and specific attacks in an ehthical and allowed way, which will be our target here. 

First, our Kali VM was ran on an oracle virtualbox VM , and given the necessary allocated RAM and storage to perform smoothly. 

Kali will perform two types of attacks on the DVWA:

1.An SSH bruteforce attack using the Hydra tool. This attacks uses the hydra tool to try a huge set of selected passwords put in a dictionary/wordlist against a specific protocol 

(SSH, HTTP-GET,HTTP-POST,SQL, and much more). This attack will trigger an alert on the WAZUH dashboard that a bruteforce attack is being launched from a certain IP-address.

2.An SQL injection attack where a malicious SQL code will be entered from my Kali machine into an input form (user input form) to pass the SQL logic and get into the database.

The DVWA is built exactly to sanitize the input badly. This will definitely show up on the WAZUH dashboard and give an alert showing an attempted SQL injection by an attacker.

