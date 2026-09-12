Another noticeable challenge was configuring the ossec.conf file in the "ossec-agent" folder.The goal was to link the Apache logs of the DVWA into the Wazuh Manager,

and the problem was that the file wouldn't open due to unaquired admin privileges. That issue was solved by copying the file to the Desktop (which will solve the admin

warning screen) then applying the necessary changes , and then recopying the file to the "ossec-agent" folder and replace it with the old one.  

