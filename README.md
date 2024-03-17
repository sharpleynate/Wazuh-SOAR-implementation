# Security Monitoring and Incident Response Implementation with Wazuh and TheHive

This project focuses on integrating Wazuh and TheHive platforms to enhance security monitoring and incident response capabilities.

# What is Wazuh?

Wazuh is an open-source security monitoring platform that provides intrusion detection, log analysis, and vulnerability detection capabilities, helping organizations to detect and respond to security threats in real-time.

# What is TheHive?

TheHive is a scalable and collaborative security incident response platform that allows security teams to efficiently manage and investigate security incidents, facilitating coordination, analysis, and remediation efforts across the organization.

# Part 1

With my virutal enviorment already set up (VMWare), I head over to https://www.microsoft.com/en-us/software-download/windows10 to download the Windows 10 ISO. 
Next, I download Sysmon and the configuration repository. https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
After that, I dragged the sysmon configuration file and placed it into the extracted sysmon directory. 
From here, I run admin for Powershell, navigating to the sysmon directory and running the executable: .\Sysmon64.exe -i '.\sysmon config.xml' to install. 

I sign into my DigitalOcean account and start setting up my droplet configs, via Ubuntu LTS. 
![Screenshot 2024-03-15 230843](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/c97e4807-95fc-4a36-87cc-8fc936945d4e)

Through the configuration process, I set up a password for my droplet, as well as setting up a firewall so I'm not spammed by external scanners. 
After that, I connect my firewall to its designated server.
![Screenshot 2024-03-15 231502](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/ae816ccb-afc2-4797-98c8-e474359802b5)
![Screenshot 2024-03-15 231717](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/88991aca-1aa3-47bc-912e-45fc68fcadf2)

I SSH into my Wazuh server to conduct updates with this command, apt-get update && apt-get upgrade -y
![Screenshot 2024-03-15 232532](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/c83b33bb-07a1-42bc-9787-87f3b26f847d)

After conducting the update and upgrade installation, I run curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a to begin with Wazuh. 
Now that I wazuh is up and running, I start by adding a new droplet, TheHive server. Lastly, I download the prerequestites for this server.

**Dependences**
apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl  software-properties-common python3-pip lsb-release

**Install Java**
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"

**Install Cassandra**
wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra

**Install ElasticSearch**
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list

sudo apt update
sudo apt install elasticsearch

After this is completed, I install TheHive. 
**Install TheHive**
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
sudo apt-get update
sudo apt-get install -y thehive

![Screenshot 2024-03-15 234437](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/0027fc1d-b77c-4a67-8aa2-57ff84e98264)
![Screenshot 2024-03-15 235106](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/ca38ed4c-c13f-489a-8014-234b0213041f)
![Screenshot 2024-03-15 235930](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/28077570-4dfa-4747-98d4-87945fd0c676)
![Screenshot 2024-03-16 000202](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/096f9b45-792f-45d3-a715-a3f9b717dde3)
![Screenshot 2024-03-16 000546](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/d35b0aa5-bc1a-408c-a170-8e36d579833d)

# PART 2

I nano into the cassandra.yaml file and configure my name, listen address, rpc address and seed. I follow that up by also configuring and enabling cassandra and elasticsearch. Once that is finished, I check status for both servers to make sure they're running. 
![Screenshot 2024-03-16 001154](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/aa5957ca-abf4-487f-8225-404f375f6f8b)

I configure yml files for both cassandra and elasticsearch to enable them active and give reading and writing perms to 'thehive' with  chown -R thehive:thehive /opt/thp command.
![Screenshot 2024-03-16 211740](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/8f0433f4-1cad-48ed-858a-a19b4c3b3c1f)
![Screenshot 2024-03-16 210949](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/66319c7b-2b0f-4fc6-a304-2fb25b576ba4)
![Screenshot 2024-03-16 211619](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/15695e6b-83c0-4592-acf6-bdecb200ecbe)

I configure the application.conf in the hive with nano /etc/thehive/application.conf command to point the server to my public ip and set to corresponding name. This will ensure connectivity to thehive server upon starting and enabling the server. Now with the default creditials I sign into TheHive. 
![Screenshot 2024-03-16 212056](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/063b47c1-55d0-4f32-98f2-990b0385ee9f)
![Screenshot 2024-03-16 212911](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/ee4e17bd-bb3b-463b-ae65-6f6ceaab114e)

I log into Wazuh and add an agent with my appropriate configurations and install the agent via my windows vm with the following command,
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.3-1.msi -OutFile ${env.tmp}\wazuh-agent; msiexec.exe /i ${env.tmp}\wazuh-agent /q WAZUH_MANAGER='143.110.231.250' WAZUH_AGENT_NAME='mydfir' WAZUH_REGISTRATION_SERVER='143.110.231.250'. After this I start the service by running the net start wazuhsvc command. Upon checking 'services' I can see that Wazuh agent has been successfully installed and running. 
![Screenshot 2024-03-16 213422](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/999ccb0a-0086-4df2-a01c-ffd1f35a9385)
![Screenshot 2024-03-16 213913](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/1ae530a9-e0ff-45a1-8397-cbb7047a5d2c)
![Screenshot 2024-03-16 214104](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/54841288-bb20-435d-861d-4ca1eeef6538)

# PART 3

I head into my ossec.conf file to add another log analysis, in this case, I'll be adding Microsoft-Windows-Sysmon/Operational. After this, I restart Wazuh server in service application. I create a Downloads folder exclusion to download mimikatz. Once that is done, I run powershell to excute mimikatz in the terminal and check to see any events related to mimikatz. 
![Screenshot 2024-03-16 215920](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/540ba9f0-ede2-4414-9ae7-01393b17b7e5)
![Screenshot 2024-03-16 215858](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/c9deed24-c289-4bf4-b997-a92231448f9d)
![Screenshot 2024-03-16 220624](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/b2856b88-6c58-4c0d-9eb5-f9d8c4f7a022)

Due to the fact my sysmon events did not trigger any alerts or rules from Wazuh. By default, Wazuh does not log everything and only logs things when a rule or alert was triggered. I proceed to change this behavior by going into the wazuh manager and configuring the ossec.conf file. I go into ossec.conf and change both 'logall' to yes. I change the filebeat to 'true' and restart wazuh-manager. I nagivate over to the wazuh dashboard and setup a new index named 'wazuh-archives-**'. Within executing mimikatz in my windows vm, I can see my wazuh archives index picked it up. 
![Screenshot 2024-03-16 220749](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/e5577631-6915-4380-ab28-d366036f98d1)
![Screenshot 2024-03-17 012129](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/f981d193-d9e6-49db-b99c-5353c081b9f1)
![Screenshot 2024-03-17 012714](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/f9918147-93d3-4667-bdd4-a6e724c3c868)
![Screenshot 2024-03-17 014108](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/eff3b672-27f3-4094-b135-3fbc478a2754)
![Screenshot 2024-03-17 015821](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/34266094-3678-4ab8-86f0-9306eff46d85)

I proceed to adding sysmon_event1 rule in local_rules.xml, changing the id, descrition and type. Upon submitting this new update, I restart the manager.
After changing the mimikatz filename and executing mikikatz, we see that sysmon has detected and logged the event. 
![Screenshot 2024-03-17 021237](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/dcdb1636-d9af-40c7-bc69-fde0b9de98dd)
![Screenshot 2024-03-17 023701](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/5032e468-00ae-480d-a5e3-da2b2ca4e53d)

# In Conclusion

The implementation process involved setting up Wazuh, an open-source security monitoring platform, along with TheHive, a collaborative security incident response platform. Initial steps included configuring virtual environments and downloading necessary tools like Sysmon and dependencies. Subsequently, server setups for Wazuh and TheHive were conducted, along with installations of associated components such as Java, Cassandra, and Elasticsearch. Agents were added to Wazuh for monitoring, and custom configurations were applied to enhance log analysis and event detection capabilities. Adjustments were made to ensure proper logging behavior in Wazuh, and additional rules were created to detect specific security events. Overall, the implementation aimed to bolster security monitoring and incident response capabilities within the organization's infrastructure.

