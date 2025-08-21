# Building a SOC + Honeynet in Azure with Live Traffic

## Introduction

For this project, I created a scale [honeynet](https://www.okta.com/identity-101/honeynet/#:~:text=Is%20a%20Honeynet?-,Definition%2C%20Usage%20&%20the%20Honeynet%20Project,weak%20passwords%2C%20and%20phishing%20attacks.&text=A%20honeynet%20is%20a%20network,resides%20on%20a%20single%20server.) in Microsoft Azure. I ingested logs from various resources into an Azure Log Analytics Workspace (LAW) and used Microsoft Sentinel to create incidents, trigger various alerts, and build attack maps. Using KQL Query, I measured security metrics in the insecure environment for 24 hours. I then applied security controls to harden the environment, measured metrics for another 24 hours and compared the results.


![363405131-b6f385a6-3b8f-4eb2-a3f4-759adb34e7fc](https://github.com/user-attachments/assets/c2eca427-e1a4-450d-adb9-8b64c72223c9)


Using Microsoft Azure on my host computer, I created two Virtual Machines (VM); one with a Windows OS, and one with a Linux OS. I installed a SQL server on the Windows VM and disabled the firewall. A third VM (‘Attack-VM’) was created to simulate attacks on the honeynet.

I took the following steps to build the honeynet:
-	Configured Network Security Groups (NSG) to allow all inbound traffic.
-	Created several users with Microsoft Entra ID and assigned them different levels of permissions.
-	Set up Log Analytics Workspace as my central log repository.
-	Created an Azure ‘Blob’ Storage account and uploaded ‘IP-watchlist’ data.
-	Enabled Microsoft Defender for Cloud to monitor security in the Azure environment.
-	Activated a key vault and created some secrety secrets
-	Configured Microsoft Sentinel with several rules to trigger alerts for specific security violations
-	Configured all resources to generate logs and deposit them into the Log Analytics Workspace.


## Architecture Before Hardening / Security Controls
For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet.




![366794114-6eaabee4-606d-41c8-923f-e9c13c63cf58](https://github.com/user-attachments/assets/f8fdc64f-38e0-4a70-bbe3-99f5b81a1583)




While the environment was wide open to the public, I used the 'Attack-VM' to simulate a brute-force attack by attempting multiple log-ins with incorrect credentials, and eventually succeeding a log-in with the correct credential. Additionally, I uploaded a simulated malware file and accessed the Azure Key Vault with insufficient permissions. All these events would trigger an alert based on the rules created within Sentinel.


## Incident Response and Remediation
After measuring and logging security metrics for 24hrs, I analyzed the logs using Log Analytics Workspaces and alerts using Sentinel and observed that the brute-force attempt AND success indeed created the proper logs and alerts. The malware injection and improper access to the key vault equally created the proper alerts.

To 'harden' the environment, I:
- reactivated the firewalls to restrict access and protect the resources from unauthorized connections,
- reconfigured the Network Security Groups to restrict all access with the exception of my own IP-address,
- replaced all the resources' public endpoints with private endpoints to allow access from the internal subnet only,
- utilized the Microsoft Defender for Cloud and NIST 800-53 frameworks for security recommendations and regulatory compliance.

## Architecture After Hardening / Security Controls



![366794598-e62f66a3-02ab-4d15-814e-1315cc76f8a6](https://github.com/user-attachments/assets/52173322-dae6-419c-b0d7-46237da9f193)


Once the Security Controls were implemented, the Virtual Machines (now residing inside the private virtual net), security metrics we're once again collected for a 24hr period with the results provided in the diagram below.



## Metrics measured BEFORE and AFTER Hardening/Security Controls (24hr capture)
The following chart displays the measured metrics from a 24hr period before and after applied security controls and the change in %.


![image](https://github.com/user-attachments/assets/ed5c88f6-1aaf-4160-ac7d-1384ca53c450)


## Attack Maps BEFORE Hardening / Security Controls

The following 3 images are a visual representation of the locations from where the attacks generated (cross-referenced with the 'IP-watchlist' in the Blob storage account). 

Failed attempts to 'remote-desktop' (RDP) into the Windows-VM
![windows-rdp-auth-fail BEFORE](https://github.com/user-attachments/assets/118ddb60-2f6a-4b3e-8979-5fd47905cc0e)


Failed attempts to 'Secure Shell' (SSH) into the Linux-VM.
![linux-ssh-auth-fail BEFORE](https://github.com/user-attachments/assets/c6eef7da-813c-4e9b-a7f5-7a1e9d5d92d6)


All malicious traffic flowing through the Network Security Groups 
![image](https://github.com/user-attachments/assets/aa335341-05ca-42b9-8812-42a0b7fea0d2)



## Attack Maps AFTER Hardening / Security Controls

```(Sentinel did NOT generate ANY maps for any of the KQL map queries for the metrics measured during the 24hr period after hardening thus confirming the effectiveness of the Security Controls implemented.)```


## Conclusion

In this project, I created a scale honeynet in Microsoft Azure and directed logs generated by various sources into a Log Analytics Workspace. I employed Microsoft Sentinel to trigger alerts and create incidents based on the ingested logs.

By adhering to NIST 800-61 guidelines and implementing security controls outlined in NIST 800-53, I effectively managed high priority incidents. Incidents were assessed and categorized by their severity and investigated to determine whether they were "true positives". I documented post-incident activities and resolutions performed, and their subsequent effectiveness. Metrics were measured in the environment before and after security controls were applied, and the number of security events and incidents were drastically reduced (in some cases completely eliminated) after the security controls were applied, demonstrating the effectiveness of their implementation.

(Figures 1, 2, and 3, created by [Francisco Fonseca](https://www.linkedin.com/in/franciscovfonseca/))
