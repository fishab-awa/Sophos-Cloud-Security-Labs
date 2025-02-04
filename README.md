# Sophos XG Firewall Configuration Labs

## Overview
This series of labs covers the deployment, configuration, and management of Sophos XG Firewall in an Azure environment. The labs will guide you through setting up a secure and efficient firewall infrastructure using Sophos XG, including management, web protection, and web server security.

Each lab is designed with specific objectives, and by the end of each one, you will have successfully completed configurations that ensure your environment is secure and well-monitored.

## Labs Overview

### Lab 1: Sophos XG Firewall Basic Configuration
**Objectives:**

- Deploy a standalone Sophos XG firewall in Azure.
- Activate the Sophos XG Firewall License (BYOL Only).
- Enable logging, modify the gateway failover rule, update firmware, and adjust the network security group settings for the firewall.

In this lab, you will start by configuring the basic functionalities of the Sophos XG Firewall, such as enabling logging, configuring failover rules, updating the firmware, and modifying the network security group.

### Lab 2: Advanced Routing and VPN Configuration
**Objectives:**

- Create a new management subnet and deploy a Windows server.
- Configure custom route tables and associate them with subnets.
- Set up routing, Clientless VPN, and NAT for outbound internet traffic.
- Verify access to the management server and check traffic flow through the Sophos XG.

This lab focuses on advanced network routing and securing remote access. You will configure a management subnet, set up a VPN for clientless access, and ensure proper routing and NAT for outbound internet traffic.

### Lab 3: Web Protection Configuration
**Objectives:**

- Apply a web filtering policy to outbound traffic.
- Verify outbound web protection functionality.
- Monitor web filtering logs using the LogViewer console.

Lab 3 will guide you through the process of configuring web protection on your Sophos XG Firewall, applying a web filtering policy, and monitoring traffic through the LogViewer. This ensures that your network is protected from harmful web traffic and helps you monitor blocked content.

### Lab 4: Web Application Firewall (WAF) Configuration
**Objectives:**

- Install a web server on a management server.
- Configure Sophos XG WAF protection for the backend web server.
- Verify the web server protection functionality, including SQL Injection and XSS protection.

In this final lab, you will configure the Sophos XG Firewall’s Web Application Firewall (WAF) to secure a backend web server. This includes setting up protection for web applications, detecting SQL injection, XSS attacks, and monitoring traffic through the firewall.

## Lab Requirements
To complete these labs, ensure the following prerequisites:

- Azure account with sufficient permissions to create resources such as virtual machines, subnets, and security groups.
- Sophos XG Firewall virtual appliance deployed in the Azure environment.
- Basic understanding of networking concepts, including routing, VPNs, and firewall rules.

## Key Learning Outcomes
By the end of these labs, you will have:

- Deployed and configured Sophos XG Firewall in an Azure environment.
- Configured routing and VPN access for secure network communication.
- Applied web filtering and WAF protections to safeguard your network.
- Gained hands-on experience with Sophos XG’s advanced features for managing network security.

Feel free to follow each lab sequentially to build on your knowledge and configurations. For further resources and documentation, refer to the Sophos XG Firewall Documentation and Azure Virtual Machines guides.
