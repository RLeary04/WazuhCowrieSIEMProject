# Procedure
___
Requirements: Wazuh Manager on another system with a static IP address 

[Steps to set static IP in Wazuh Manager](https://github.com/RLeary04/WazuhCowrieSIEMProject/blob/main/Steps%20to%20set%20static%20IP%20in%20Wazuh%20Manager.md)
![Wazuh Manager IP](https://github.com/RLeary04/WazuhCowrieSIEMProject/blob/main/WazuhManagerIP.png?raw=true)

1. Create Ubuntu Server (version 24.04 LTS) instance in virtual box
2. In the Ubuntu server, create a new user to run Cowrie under
	 `sudo adduser --disabled-password cowrie`
3. Update and install dependencies
	`sudo apt update`
	`sudo apt install git python3-virtualenv libssl-dev libffi-dev build-essential libpython3-dev python3-minimal authbind -y`
4.  Clone cowrie as the cowrie user
	`sudo su - cowrie`
	`git clone https://github.com/cowrie/cowrie.git ~/cowrie`
5. Change working directory to the cowrie user directory to create the virtual environment and install dependencies within the python virtual environment
	`cd ~/cowrie`
	`virtualenv --python=python3 cowrie-env`  
	`source cowrie-env/bin/activate ` 
	`pip install --upgrade pip`
	`pip install -r requirements.txt`
6. Copy the cowrie.cfg.dist file as cowrie.cfg in the etc directory and edit the configuration for hostname, endpoints, logging output, etc.
	 `cp etc/cowrie.cfg.dist etc/cowrie.cfg`
	 `nano etc/cowrie.cfg`
7. Run Cowrie and verify
	 `cowrie start`
	 `cowrie status`
8. Forward traffic from port 22 to 2222
		Using iptables
			`sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222`
9. Ensure port 2222 is open and listening for Wazuh
	`nc -zv YOUR_SERVER_IP 2222`
10. Install  Wazuh Agent 
	As root: 
		`curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg`
		`echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list`
		`sudo apt update`
		`sudo apt install wazuh-agent -y`
11. Register Wazuh agent with Manager
		`sudo /var/ossec/bin/agent-auth -m YOUR_WAZUH_MANAGER_IP -A honeypot-cowrie`
12.  Edit the agent configuration file (/var/ossec/etc/ossec.conf) to monitor for Cowrie logs
		`<localfile>`
			`<location>/home/cowrie/cowrie/var/log/cowrie/cowrie.json</location>`
			`<log_format>json</log_format>  
		`</localfile>`
13. Restart Wazuh agent
		`sudo systemctl restart wazuh-agent`
14. Verify Agent is running in Wazuh Manager

![Wazuh Agent Installed](https://github.com/RLeary04/WazuhCowrieSIEMProject/blob/main/WazuhAgentInstalled.png)


## Completed!

![Wazuh Dashboard](https://github.com/RLeary04/WazuhCowrieSIEMProject/blob/main/WazuhDashboard.png)
