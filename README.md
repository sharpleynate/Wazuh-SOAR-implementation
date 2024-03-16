# Wazuh-SOAR-implementation

In this project, we're setting up Wazuh with a minimum of one Agent for monitoring and security purposes. We're also incorporating a free SOAR (Security Orchestration, Automation, and Response) platform like Shuffle into the system. Our aim is to enable basic automation tasks, particularly focusing on data enrichment.

# What is Wazuh?

Wazuh is an open-source security monitoring platform that provides intrusion detection, log analysis, and vulnerability detection capabilities, helping organizations to detect and respond to security threats in real-time.

# What is TheHive?

TheHive is a scalable and collaborative security incident response platform that allows security teams to efficiently manage and investigate security incidents, facilitating coordination, analysis, and remediation efforts across the organization.

# PART 1 

I focus my efforts in making a clear and comprehsenive graph of the architueral 
![image](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/9a168a0e-f721-4339-ba1b-88ead5a14537)

# Part 2

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
![Screenshot 2024-03-16 000359](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/a2bb1d83-dd97-4885-a8cd-3ce69664dc27)
![Screenshot 2024-03-16 000546](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/d35b0aa5-bc1a-408c-a170-8e36d579833d)


