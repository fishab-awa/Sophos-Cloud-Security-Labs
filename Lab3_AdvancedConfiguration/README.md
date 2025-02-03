# Configure Sophos XG Web Protection in Azure - Lab 3

## Objectives

Upon successful completion of this lab, you will be able to:

- Apply a web filtering policy to outbound traffic.
- Verify outbound web protection functionality.
- Monitor web filtering logs using the LogViewer console.

---

## Task 1 – Apply a Web Filtering Policy to Outbound Traffic

1. Log into the WebAdmin of the **"sophosxgAzureFw01"** firewall:  
   `https://<public-ip-address>:4444`  
   *(Use the public IP address noted in Lab 1, Task 2, Step 5.)*



   ![WebAdmin Login](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab2/routing_config2.png)

2. In the **Advanced** section, configure the following:
   - **Setting:** Web Policy
   - **Value:** Default Workplace Policy
   - Click **Save**.

3. In WebAdmin, go to:  
   **Protect** → **Firewall** → Edit the **"vnet_to_internet"** rule.

4. In later versions of the XG Firewall, navigate to:  
   **Protect** → **Rules and Policies** → From ‘Traffic to WAN’ grouping, select **"vnet_to_internet"** (the firewall rule created in the previous lab).

   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/firewall-rule-config1.png)
   ![Firewall Rule Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/firewall-rule-config2.png)

5. Open **LogViewer** (upper right corner of WebAdmin):  
   - A new window will open with the **New Unified LogViewer console**.
   - In the LogViewer console, select **Webfilter** and leave the window open for monitoring.

   ![LogViewer Console](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/logviewer-console1.png)
   ![LogViewer Console](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/logviewer-console2.png)

---

## Task 2 – Verify Outbound Web Protection Functionality

1. Log into the **User Portal** of the **"sophosxgAzureFw01"** firewall:  
   `https://<public-ip-address>` *(without port 4444)*

   - **Username:** `remoteadmin`  
   - **Password:** `SophosAzurePoCPass01`

   ![User Portal Login](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/user-portal-login.png)

2. In the **User Portal**, click on **"Management Server (RDP)"** to connect to the backend management server.  

3. In **Server Manager** → **Local Server**, set **"IE Enhanced Security Configuration"** to **Off** and click **OK**.

   ![Server Manager Settings](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/server-manager-setting.png)

4. Open **Internet Explorer** and browse to:  
   `http://sophostest.com/` → You should be able to access this page.

   ![Sophos Test Site](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/logviewer-denied-message1.png)

5. Scroll down, right-click on **"Gambling"**, and open it in a new tab → You should see a **block page**.

6. Repeat step 5 for the **"Criminal Activity"** page → You should see a **block page**.
   ![Blocked Access Screenshot](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/logviewer-denied-message2.png)

7. Return to the **open LogViewer** window:
   - You should see that the sites are **"Denied"**.
   - You may need to click on **Refresh** to see the **Denied** messages.

   ![LogViewer Denied Messages](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/logviewer-denied-message4.png)

> **Issue Found:**  
> When clicking on the **Gambling** category, access was **not blocked**. Other non-work-related categories (illegal drugs, weapons) were correctly blocked.  
> Possible cause: The page was cached in the browser before applying the correct web policy.

   ![Gambling Not Blocked Screenshot](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab3/logviewer-denied-message3.png)

---

## Summary

In this lab, we successfully applied a **web filtering policy** to outbound traffic, verified its **effectiveness**, and monitored blocked traffic in the **LogViewer console**. Further investigation is needed for caching-related issues when applying new policies.

---

