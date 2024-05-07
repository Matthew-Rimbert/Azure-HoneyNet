# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

# Introduction

In this project, I developed a mini honeynet in Azure, pulling in log sources from various resources into a Log Analytics workspace. This setup feeds into Microsoft Sentinel, which is used to construct attack maps, trigger alerts, and create incidents. I conducted an initial measurement of security metrics in the unsecured environment over 24 hours, followed by implementing security controls to fortify the environment. After applying these controls, I measured the metrics again for another 24 hours and this is what followed:
## Tools & Technology Used
- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

``` For the "BEFORE" metrics, all resources were originally deployed and exposed to the Internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources were deployed with public endpoints visible to the Internet—aka, there was no use for Private Endpoints. ```

``` For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint ```
<div align="center">

## Attack Maps Before Hardening / Security Controls

</div>

![Before_linux-ssh-auth-fail_24Hours](https://github.com/Matthew-Rimbert/Azure-HoneyNet/assets/169205418/a9f78599-fd30-4198-abb1-b896b3503424)<br>
---
![Before_mssql-auth-fail](https://github.com/Matthew-Rimbert/Azure-HoneyNet/assets/169205418/ab68b5a2-b56e-47d4-b780-c7e9ec04d422)<br>
---
![Before_nsg-malicious-allowed-in](https://github.com/Matthew-Rimbert/Azure-HoneyNet/assets/169205418/fc1c11fd-b0b5-4045-a897-e1f4e740576b)<br>
---
![Before_windows-rdp-auth-fail](https://github.com/Matthew-Rimbert/Azure-HoneyNet/assets/169205418/8827c7d9-f0c6-4681-84e4-4643a530597a)<br>
---
<div align="center">

## Collection of Incidents

<div/>

![After_24_Incidents](https://github.com/Matthew-Rimbert/Azure-HoneyNet/assets/169205418/88c3e290-cd67-4be2-b956-6b194a540de4)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
<div align="center">
  
#### Start Time: May 1 2024 - 7:00:00 AM 
#### Stop Time: May 2 2024 - 7:00:00 AM
| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 15079
| Syslog                   | 4134
| SecurityAlert            | 0
| SecurityIncident         | 78
| AzureNetworkAnalytics_CL | 1300

</div>

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
<div align="center">
  
#### Start Time: May 6 2024 - 6:00:00 PM 
#### Stop Time:	May 7 2024 - 6:00:00 PM
| Metric                   | Count |
| ------------------------ | ----- |
| SecurityEvent            | 11832 |
| Syslog                   | 1     |
| SecurityAlert            | 0     |
| SecurityIncident         | 0     |
| AzureNetworkAnalytics_CL | 0     |

</div>

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)
# Conclusion
```All map queries actually returned no results due to no instances of malicious activity for the 24-hour period after hardening.```

In this project, we constructed a mini honeynet within Microsoft Azure and integrated log sources into a Log Analytics workspace. Microsoft Sentinel was used to generate alerts and incidents based on these logs. We also tracked metrics in an initially insecure environment, both before and after applying security measures. Notably, the application of security controls significantly reduced the number of security events and incidents, underscoring their effectiveness.

### Additional Information
Next, I will showcase screenshots where I deliberately trigger specific alerts. This will illustrate the additional security measures we have incorporated and demonstrate how each alert responds under controlled conditions.
It's important to mention that if there had been heavy usage by regular users within the network, it's probable that more security events and incidents would have been observed within this lab.

![Privilege Escalation (Azure Key Vault)   Password Reset   Lateral Movement](https://github.com/Matthew-Rimbert/Azure-HoneyNet/assets/169205418/f13645ec-d74d-4a39-addd-c5c62655f708)

1. Unauthorized Access to Key Vault: Triggers when there is unauthorized viewing of the global admin password stored in a Key Vault, which is restricted to a strict need-to-know basis.<br>
2. Excessive Password Resets: Alerts are generated in cases of excessive password resets, a common method employed by threat actors aiming to establish backdoors through the creation of multiple compromised accounts.<br>
3. Host Firewall Tampering: Monitors and alerts when modifications to firewall configurations exceed established security protocols, indicating potential unauthorized tampering
