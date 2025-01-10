# Sophos Firewall Configuration - Lab 2

## Objectives

Upon successful completion of this lab, you will be able to:

1. Create a new management subnet
2. Deploy a Windows server into the new management subnet
3. Create a custom route table (with user-defined routes)
4. Attach the route table to the appropriate subnet
5. Configure routing on the Sophos XG Firewall
6. Configure Clientless VPN on the Sophos XG Firewall to the management server
7. Configure the Sophos XG Firewall to allow (and NAT) Outbound Internet Traffic from the internal subnets
8. Verify that you can access the management server
9. Verify that the traffic is going through the Sophos XG

---

## Task 1 - Create a New Management Subnet

1. Open an Internet browser and navigate to the Azure Portal: [https://portal.azure.com](https://portal.azure.com).
2. In the upper left corner, click **All resources**.
3. In the **All resources** blade, click on **sophosxg-azure-vnet**.
4. Under the **Settings** section, click on **Subnets**.
5. In the **sophosxg-azure-vnet - Subnets** blade, click on **+ Subnet** to add a new subnet.

    **Add subnet settings:**

    | Setting                | Value              |
    |------------------------|--------------------|
    | Name                   | management-subnet  |
    | Address range          | 10.10.253.0/24     |
    | Network security group | None               |
    | Route table            | None               |
    | Service endpoints      | None selected      |

6. Click **OK**.

   ![Create Subnet](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/create_subnet.png)

---

## Task 2 - Deploy a Windows Server into the New Management Subnet

1. In the upper left corner, click **Create a resource**.
2. In the **New** blade, type **Windows Server** into the search box and press **Enter**.
3. In the **Everything** blade, click on **Windows Server 2016 Datacenter**.
4. In the **Windows Server 2016 Datacenter** blade, ensure the deployment model is **Resource Manager**, then click **Create**.

   ![Windows Server Deployment](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/deploy_server1.png)
   ![Windows Server Deployment](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/deploy_server2.png)

6. In the **Create virtual machine** blade, configure the following settings:

    **Basic settings:**

    | Setting          | Value                          |
    |------------------|--------------------------------|
    | Name             | mgmt-srv-1                     |
    | VM disk type     | SSD                            |
    | Username         | azureadmin                     |
    | Password         | SophosAzurePoCPass01           |
    | Confirm password | SophosAzurePoCPass01           |
    | Subscription     | Select your subscription       |
    | Resource group   | Use existing → sophosxg-poc-azure-rg |
    | Location         | Automatically populated        |
    | Save money       | No                             |

   ![VM Basic Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/VM_settings.png)

7. In the **Settings - Configure optional features** blade, configure the following:

    **Optional settings:**

    | Setting               | Value                      |
    |-----------------------|----------------------------|
    | Storage               | Default (Use managed disks)|
    | Network               | Virtual network: sophosxg-azure-vnet |
    | Subnet                | management-subnet          |
    | Public IP address     | None                       |
    | Network security group| None                       |
    | Other Settings        | Leave as default           |

   ![VM Optional Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/VM_optional_settings1.png)
   ![VM Optional Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/VM_optional_settings2.png)

---

## Task 3 - Create a Custom Route Table (with User-Defined Routes)

1. In the upper left corner, click **New**.
2. In the **New** blade, type **Route Table** into the search box and press **Enter**.
3. Click on **Route Table**, then click **Create**.
4. In the **Create route table** blade, configure the following:

    | Setting        | Value                           |
    |----------------|---------------------------------|
    | Name           | management-subnet-routetable    |
    | Subscription   | Select your subscription        |
    | Resource group | Use existing → sophosxg-poc-azure-rg |
    | Location       | Same as deployed resources      |

5. Click **Create**.

   ![Create Route Table](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/create_route_table1.png)
   ![Create Route Table](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/create_route_table2.png)
   ![Create Route Table](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/create_route_table3.png)

7. Navigate to **Route tables**, select **management-subnet-routetable**.
8. Under **Settings**, click on **Routes**, then click **Add**.

    **Add route settings:**

    | Setting        | Value             |
    |----------------|-------------------|
    | Route name     | subnet-route      |
    | Address prefix | 10.10.253.0/24    |
    | Next hop type  | Virtual network   |
    | Next hop address | Empty           |

9. Click **OK**.

   ![Add Route](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/add_route1.png)
   ![Add Route](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/add_route2.png)

11. Create another route table using similar steps with different configurations:

    **Additional route settings:**

    | Setting        | vnet-route       | Internet-route   |
    |----------------|------------------|------------------|
    | Route name     | vnet-route       | Internet-route   |
    | Address prefix | 10.10.0.0/16     | 0.0.0.0/0        |
    | Next hop type  | Virtual appliance| Virtual appliance|
    | Next hop address | 10.10.1.4       | 10.10.1.4        |

   ![Add Route](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/additional_route1.png)
   ![Add Route](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/additional_route2.png)
   ![Add Route](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/additional_route3.png)
---

## Task 4 - Attach the Route Table to the Appropriate Subnet

1. In the **management-subnet-routetable** blade, under **Settings**, click on **Subnets**.
2. In the **Subnets** blade, click on **Associate**.
3. Select **sophosxg-azure-vnet** and choose **management-subnet**.

   ![Associate Subnet](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/associate_subnet.png)

---

## Task 5 - Configure Routing on the Sophos XG Firewall

We need to configure the Sophos XG firewall to route traffic going to our internal subnets out of its LAN interface.

1. Log into the WebAdmin of the "sophosxgAzureFw01" firewall: `https://<public-ip-address>:4444`.
2. In the WebAdmin, navigate to **Configure** → **Routing** → **Static Routing**.
3. Under **IPv4 Unicast Route**, click **Add**.
4. In the **Add Unicast Route** window, configure the following:

    | Setting        | Value                     |
    |----------------|---------------------------|
    | Destination IP | 10.10.0.0                 |
    | Netmask        | /16 (255.255.0.0)         |
    | Gateway        | 10.10.1.1                 |
    | Interface      | PortA                     |
    | Distance       | Leave default setting (0) |

   **Note:** To get the public IP address of the Sophos firewall, you can simply navigate to virtual machines from the home menu.
   ![Routing Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/routing_config1.png)
   ![Routing Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/routing_config2.png)
   ![Routing Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/routing_config3.png)
   ![Routing Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/routing_config4.png)
   ![Routing Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/routing_config5.png)
   

---

## Task 6 - Configure Clientless VPN on the Sophos XG Firewall to the Management Server

> **Note:** A Clientless VPN allows users to securely access network resources over the internet without needing dedicated VPN client software.

1. In the WebAdmin, navigate to **Configure** → **Authentication**.
2. Create a new user with the following settings:

    | Setting        | Value                     |
    |----------------|---------------------------|
    | Username       | remoteadmin               |
    | Name           | Remote Admin              |
    | Password       | Sophos@AzurePoCPass01     |
    | User Type      | User                      |
    | Email          | Enter your email address  |
    | Group          | Open Group                |

3. Save the configuration.

   ![User Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/user_config.png)

4. Navigate to **Configure** → **Remote Access VPN** → **Clientless SSL VPN policy** → **Bookmarks** → **Add**.
5. In the **Add Bookmark** window, configure the following:

    | Setting          | Value                |
    |------------------|----------------------|
    | Name             | Management Server    |
    | Type             | RDP                  |
    | URL              | 10.10.253.4          |
    | Automatic Login  | On                   |
    | Username         | azureadmin           |
    | Password         | SophosAzurePoCPass01 |
    | Protocol Security| NLA                  |

6. Save the bookmark.

   ![Bookmark Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/bookmark_config1.png)
   ![Bookmark Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/bookmark_config2.png)

8. Navigate to **Policies**, then click **Add**.
9. Configure the following:

    | Setting            | Value             |
    |--------------------|-------------------|
    | Name               | Management Access |
    | Policy Members     | Open Group        |
    | Published Bookmarks| Management Server |

10. Apply the policy.

   ![Policy Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/policy_config1.png)
   ![Policy Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/policy_config2.png)
   ![Policy Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/policy_config3.png)

---

## Task 7 - Configure the Sophos XG Firewall to Allow (and NAT) Outbound Internet Traffic from the Management Subnet

1. In the WebAdmin, navigate to **Protect** → **Rules and Policies** → **Add Firewall Rule** → **New Firewall Rule**.
2. Configure the following settings:

    | Setting                 | Value                                    |
    |-------------------------|------------------------------------------|
    | Rule Name               | vnet_to_internet                         |
    | Action                  | Accept                                   |
    | Source Zones            | LAN                                      |
    | Source Networks         | sophosxg-azure-vnet-10.10.0.0/16         |
    | Destination Zones       | WAN                                      |
    | Destination Networks    | Any                                      |
    | Services                | Any                                      |
    | Log Traffic             | Tick "Log Firewall Traffic"              |

   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/fire_rule_config1.png)
   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/fire_rule_config2.png)
   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/fire_rule_config3.png)
   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/fire_rule_config4.png)
   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/fire_rule_config5.png)
   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/fire_rule_config6.png)

---

## Task 8 - Verify Access to the Management Server

1. Log into the User Portal of the "sophosxgAzureFw01" firewall: `https://<public-ip-address>`.
2. Enter the following credentials:

    | Setting  | Value                     |
    |----------|---------------------------|
    | Username | remoteadmin               |
    | Password | SophosAzurePoCPass01      |

3. In the User Portal, click on **Management Server (RDP)** to connect to the backend management server.

   **Note:** Ensure User Portal access is enabled on LAN and WAN by navigating to **Administration** → **Device Access** → **WAN** → **User Portal**, then click **Apply**.

   ![User Portal Access](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/user_portal_access1.png)
   ![User Portal Access](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/user_portal_access2.png)
   ![User Portal Access](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/user_portal_access3.png)
   ![User Portal Access](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/user_portal_access4.png)
   
---

## Task 9 - Verify Traffic Through the Sophos XG

1. Log into the WebAdmin of the "sophosxgAzureFw01" firewall: `https://<public-ip-address>:4444`.
2. Click on **Log Viewer** (top right corner).
3. In the **Search Box**, type `10.10.253.4` and press **Enter**.

   You should see the traffic from the management server in the logs.

   ![Traffic Verification](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/traffic_verification1.png)
   ![Traffic Verification](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/traffic_verification2.png)

