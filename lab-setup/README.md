This section will be covering up how the SIEM setup for this project was built and explain each section and its role.

The project is divided ,and according to the files in this section are divided into 5 parts:

1.The Kali Linux VM (simulating the malicious attacker)

2.The Wazuh VM (the manager that will run, collect, manage, and analyze the events and logs of the Wazuh SIEM)

3.The Windows Agent installed on the Main windows OS that will feed the Spunk Manager with the logs and events

of the windows OS

4.The DVWA that stands for a damn vulnerable web app and will our ethical target to launch attacks on

5.The logs ingested by the Windows Agent from the DVWA(specifically Apache). This point will be discussed more in the ingested-logs.md file.

