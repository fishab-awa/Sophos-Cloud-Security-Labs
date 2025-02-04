# Sophos Firewall Configuration - Lab 4

## Objectives

Upon successful completion of this lab, you will be able to:

1. Install a Web Server
2. Configure Sophos XG WAF protection for the backend web server
3. Verify the web server protection functionality

---

## Task 1 - Install a Web Server

1. Enter the following information and click **Login**:
   - **Username**: `remoteadmin`
   - **Password**: `SophosAzurePoCPass01`
2. In the **User Portal**, click on **Management Server (RDP)** to connect to the backend management server.
3. You should now be connected to the management server.
4. In the taskbar of the management server, click on the **search button** and type `PowerShell`. Click on **Windows PowerShell** desktop app.
5. In the **Windows PowerShell** console, type the following command and press Enter:
   ```powershell
   Install-WindowsFeature -Name Web-Server -IncludeAllSubFeature -IncludeManagementTools
   ```
6. The command will install IIS and its sub-features. The installation may take about 2 minutes to complete.

   ![IIS Installation](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/iis-installation1.png)
   ![IIS Installation](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/iis-installation2.png)
   ![IIS Installation](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/iis-installation3.png)

---

## Task 2 - Configure Sophos XG WAF Protection for the Backend Web Server

1. Log into the **WebAdmin** of the "sophosxgAzureFw01" firewall: `https://<public-ip-address>:4444`.
2. In the WebAdmin, navigate to:
   - **Protect** → **Web Server** → **Web Servers** → **Add**.
3. Configure the following settings:
   - **Name**: `mgmt-srv-1`
   - **Description**: `internal web server`
   - **Host**: `mgmt-srv-1-10.10.253.4` (Create an IP object for the private IP of the management server `10.10.253.4`)
   - **Type**: `Plaintext (HTTP)`
   - **Port**: `80`
   - Leave other settings as default
   - Click **Save**

   ![Web Server Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/web-server-config1.png)
   ![Web Server Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/web-server-config2.png)
   ![Web Server Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/web-server-config3.png)
   ![Web Server Configuration](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/web-server-config4.png)

4. Go to **Protect** → **Web Server** → **Protection Policies** → **Add**.
5. Configure the following settings:
   - **Setting Name**: `webapp-protection-policy`
   - **Pass Outlook Anywhere**: `Off`
   - **Mode**: `Reject`
   - **Cookie Signing**: `On`
   - **Static URL hardening**: `Off`
   - **Form hardening**: `Off`
   - **Antivirus**: `On`
   - **Block clients with bad reputation**: `On`
   - **Common threat filter Setting**: `On`
   - Leave other settings as default
   - Click **Save**

   ![Protection Policy](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/protection-policy1.png)
   ![Protection Policy](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/protection-policy2.png)
   ![Protection Policy](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/protection-policy3.png)
   ![Protection Policy](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/protection-policy4.png)

6. Go to **Protect** → **Firewall** → **+ Add Firewall Rule** → Select **Business Application Rule**.
7. In the "Add Business Application Rule" window, configure the following:
   - **Application Template**: `Web Server Protection [WAF]`
   - **Rule Name**: `webapp_access`
   - **Description**: `Access to web server from the Internet`
   - **Rule Position**: `Top`
   - **Hosted Address**: `#PortB`
   - **HTTPS**: `Unticked`
   - **Redirect HTTP**: `Unticked`
   - **Listening Port**: `80`
   - **Domains**: Add the FQDN that you noted in Lab 1, Task 1, Step 9
   - **Web Server**: `mgmt-srv-1`
   - **Protection**: `webapp-protection-policy`
   - **Pass Host Header**: `Selected`
   - Click **Save**

   ![Firewall Rule](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/firewall-rule1.png)
   ![Firewall Rule](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/firewall-rule2.png)
   ![Firewall Rule](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/firewall-rule4.png)

---

## Task 3 - Verify the Web Server Protection Functionality

1. Open a browser and navigate to `http://<fqdn>`. Use the FQDN noted in Lab 1, Task 1, Step 9.
2. You should see the default IIS web page.

   ![IIS Default Page](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/iis-default-page.png)

3. Open the WebAdmin page of the Sophos XG Firewall: `https://<public-ip-address>:4444`.
4. In the top right corner, click on **Log Viewer**. A new window will open with the "New Unified LogViewer" console.
5. Change the log from **Firewall** to **Web Server Protection**. You should see traffic going through the Sophos XG.

   ![Log Viewer](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/log-viewer.png)

6. Open a new browser tab and go to the URL: `http://<fqdn>/test.php?id=sleep(30)`. This will trigger an SQL injection detection.

   ![SQL Injection Test](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/sql-inj-test1.png)
   ![SQL Injection Test](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/sql-inj-test2.png)

7. Optional: To test XSS detections, add the following statement to your URL: `index.php?name=<script>window.onload=function(){var link=document.getElementsByTagName("a");link[0].href="http://not-real-xssattackexamples.com/";}</script>`.

   ![XSS Test](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/xss-test1.png)
   ![XSS Test](https://github.com/fishab-awa/Sophos-Cloud-Security-Labs/blob/main/images/lab4/xss-test2.png)

---

## Conclusion

In this lab, you successfully:

- Installed a web server on the management server.
- Configured Sophos XG WAF protection for the backend web server.
- Verified web server protection functionality, including logging and threat detection.

> **Note:** If any issues arise, check the firewall logs and ensure that the correct configurations were applied.

---

## References
- [Sophos XG Firewall Documentation](https://www.sophos.com/en-us/products/next-gen-firewall.aspx)
- [Azure Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/)
- [IIS Installation Guide](https://docs.microsoft.com/en-us/iis/install/installing-iis-7/)

