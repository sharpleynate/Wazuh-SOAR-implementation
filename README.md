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

After conducting the update and upgrade installation, I run curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
to begin with Wazuh. 
![Screenshot 2024-03-15 233320](https://github.com/sharpleynate/Wazuh-SOAR-implementation/assets/114451775/3909477e-b596-4033-97a7-c7d6e3db8dce)

