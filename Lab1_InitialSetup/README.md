# Sophos Firewall Configuration - Lab 1

## Objectives

Upon successful completion of this lab, you will be able to:

- Deploy a standalone Sophos XG firewall in Azure
- Activate the Sophos XG Firewall License (BYOL Only)
- Enable logging on the XG firewall
- Modify the gateway failover rule on the XG firewall
- Update the firmware of the appliance
- Modify the default network security group of the XG firewall

> **Note:** We are skipping Task 1 and Task 2 as they were not documented.

---

## Task 3 - Enable Logging on the XG Firewall

1. After synchronizing the firewall device with the Sophos account, you will be prompted to create a secure storage master key. You can either create a master key or skip it.
2. Logging needs to be enabled to verify functionality of features in this lab.
3. Log into the WebAdmin of the "sophosxgAzureFw01" firewall: `https://<public-ip-address>:4444`.

   ![WebAdmin Login](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/sophosFW-login%20portal.png)

4. In the WebAdmin, navigate to: 
   **Configure** → **System Services** → **Log Settings**.
5. Select all logging options and click **Apply**.

   ![Log Settings Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/logging-settings.png)

---

## Task 4 - Modify the Gateway Failover Rule on the XG Firewall

1. On the main dashboard of the firewall, notice the "Interfaces" icon turned red. This occurs because the Azure vNet router does not respond to pings (refer to the [Azure Virtual Network FAQ](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-faq)).

   ![Interface Error](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/bad-interface.png)

2. In the WebAdmin, navigate to: 
   **Configure** → **Routing** → **Gateways**.
3. Under **IPv4 Gateway**, locate the "DHCP_PortB_GW" configuration, and click **Edit**.
4. In the **Gateway Detail** page, under **Failover Rules**, click **Edit**.
5. Modify the settings as follows:
   - Protocol: **TCP**
   - Port: **53**
   - IP Address: **8.8.8.8**
6. Click **Save**.

   ![Failover Rule Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/failover-config1.png)
   ![Failover Rule Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/failover-config2.png)
> You can use any host or port reachable for monitoring.
   ![Failover Rule Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/failover-config3.png)



   
---

## Task 5 - Update the Firmware of the Appliance

1. Log into the WebAdmin of the "sophosxgAzureFw01" firewall: `https://<public-ip-address>:4444`.
2. In the WebAdmin, navigate to: 
   **System** → **Backup & Firmware** → **Firmware**.
3. Under the **Latest Available Firmware** section, click **Check for new firmware**.
4. If a new firmware version is found, click **Download**.
   - If none is found, skip this task and proceed to the next lab.
5. Click **Install**.
6. The firmware update will be applied, and the appliance will reboot (this may take up to five minutes).

   ![Firmware Update Steps](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/firware-update.png)

---

## Task 6 - Modify the Default Network Security Group of the XG Firewall

In this task, modify the default network security group of the WAN NIC of the XG firewall to allow HTTP, HTTPS, RDP, UDP 500, and UDP 4500. These ports are needed to complete other parts of this lab.

### Instructions

1. Log into the Azure Portal: [https://portal.azure.com](https://portal.azure.com).
2. In the upper left corner, click **All services**.
3. In the **All services** blade, click **Resource groups**.
4. Open "sophosxg-poc-azure-rg" from the list, and click **SecurityGroup**.
5. In the **SecurityGroup** blade, under **Settings**, click **Inbound Security Rules**.
6. In the **Inbound Security Rules** blade, click **+ Add**.
7. Configure the following inbound security rules:

| Setting               | Value          |
|-----------------------|----------------|
| **Source**            | Any            |
| **Source port ranges**| *              |
| **Destination**       | Any            |
| **Destination port**  | 3389           |
| **Protocol**          | TCP            |
| **Action**            | Allow          |
| **Priority**          | 1100           |
| **Name**              | Allow_rdp      |

   ![Add Inbound Rule - RDP](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/sophos-secgrp-navgation.png)
   ![Add Inbound Rule - RDP](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/adding-inbound-rule.png)

8. Add additional rules as follows:

| Name               | Destination Port Range | Protocol | Action | Priority |
|--------------------|-------------------------|----------|--------|----------|
| allow_http         | 80                      | TCP      | Allow  | 1150     |
| allow_https        | 443                     | TCP      | Allow  | 1200     |
| allow_udp_500      | 500                     | UDP      | Allow  | 1250     |
| allow_udp_4500     | 4500                    | UDP      | Allow  | 1300     |

   ![Add Additional Rules](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab1/all-inbound-rules.png)

---



This completes Lab 1 of the Sophos Firewall Configuration.
