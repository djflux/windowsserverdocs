---
title: Manage Azure IaaS Virtual Machines with Honolulu
description: "How to manage Azure IaaS VMs with Honolulu"
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: Honolulu
ms.tgt_pltfrm: na
ms.topic:
author: coreyp-at-msft
ms.author: coreyp
manager: dongill
ms.date: 01/23/2018
---

# Manage Azure IaaS Virtual Machines with Honolulu

>Applies To: Windows Server (Semi-Annual Channel), Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows 10

You can use Project Honolulu to manage your Azure VMs as well as on-premises machines. There are several different configurations possible. This article will walk through you those options to help you decide which configuration makes sense for your scenario.

## Manage with an on-premises Honolulu gateway

If you’ve already installed Honolulu on an on-premises gateway (either on Windows 10 or Windows Server 2016), you can use this same gateway to manage Windows 10 or Windows Server 2012, 2012R2, or 2016 VMs in Azure. 

### Connecting to VMs with a public IP

If your target VMs (the VMs you wish to manage with Honolulu) have public IPs, you can add them to your Honolulu gateway by IP address, or by FQDN. There are a couple considerations to take into account:

You must enable WinRM access to your target VM by running the following in PowerShell or the Command Prompt on the target VM: `winrm quickconfig`

You must also enable inbound connections to port 5985 for WinRM over HTTP in order for Honolulu to manage the target VM:

1. Run the following PS script on the target VM to enable inbound connections to port 5985 on the guest OS:   
`‎Set-NetFirewallRule -Name WINRM-HTTP-In-TCP-PUBLIC -RemoteAddress Any`

2. You must also open the port in Azure networking:

    - select your Azure VM, select **Networking**, then **Add inbound port rule**. 
    - In the **Port ranges** field, enter **5985**.
    
    If your Honolulu gateway has a static IP, you can select to only allow inbound WinRM access from your Honolulu gateway for added security.
    To do this, select **Advanced** at the top of the “Add inbound security rule” pane.

    For **Source**, select **IP Addresses**, then enter the Source IP address corresponding to your Honolulu gateway.

    - For **Protocol** select **TCP**.
    - The rest can be left as default.

> [!NOTE]
> You must create custom port rule. The WinRM port rule provided by Azure networking uses port 5986 (over HTTPS) instead of 5985 (over HTTP). 

### Connecting to VMs without a public IP

If your target Azure VMs do not have public IPs, and wish to manage these VMs from a Honolulu gateway deployed in your on-premises network, you will need to configure your on-premises network to have connectivity to the VNet on which the target VMs are connected. There are 3 ways you can do this: ExpressRoute, Site-to-Site VPN, or Point-to-Site. [Learn which connectivity option makes sense in your environment.](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design) 

Ensure WinRM is running on your target VM(s) by running the following in PowerShell or the Command Prompt on the target VM: `winrm quickconfig`

If you run into any issues, consult [Troubleshoot Project Honolulu](https://docs.microsoft.com/en-us/windows-server/manage/honolulu/honolulu-troubleshoot) to see if additional steps are required for configuration (for example, if you are connecting using a local administrator account or are not domain joined).

## Manage with a Honolulu gateway deployed in Azure

### Deployment considerations
You can manage Azure VMs without any on-premises dependency by deploying Project Honolulu in the VNet where your target VMs are connected. 

To manage VMs outside of the VNet on which the Honolulu gateway is deployed, you must establish VNet-to-VNet connectivity between the VNet of the Honolulu gateway and the VNet of the target servers. This connectivity can be established with VNet Peering, VNet-to-VNet connection, or a Site-to-Site connection. [Learn more about which VNet-to-VNet connectivity option makes sense in your environment.](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)

### Installing Honolulu in an Azure VM
Honolulu can be installed on an existing or newly deployed VM in your environment. The VM that you choose for Honolulu installation must have a public IP and DNS name. 

Before installing Project Honolulu on your desired gateway VM, you will need to install a SSL certificate to use for HTTPS communication, or you can choose to use a self-signed certificate generated by Project Honolulu. However, you will get a warning when trying to connect from a browser if you choose the latter option. You can bypass this warning in Edge by clicking “Details” > “Go on to the webpage” or in Chrome by selecting “Advanced” > “Proceed to [webpage]”. We recommend you only use self-signed certificates for test environments.


#### Install Project Honolulu onto your desired gateway VM:
> [!NOTE]
> These instructions are for installing on full server with desktop experience. 

1. Visit [https://aka.ms/HonoluluDownload](https://aka.ms/HonoluluDownload) on your local machine to download Project Honolulu to your local machine 

2. Establish a remote desktop connection to the VM, then copy the MSI from your local machine and paste into the VM

3. Double click the MSI to begin installation and step through the installer

   - By default, the installer chooses port 443 (HTTPS), which is recommended. If you wish to select a different port, note that you will need to open that port in your firewall as well. 

   - If you have already installed an SSL certificate on the VM, ensure you select that option and enter the thumbprint

4. Start the Project Honolulu service (run C:/Program Files/Project ‘Honolulu’ (Technical Preview)/sme.exe)

[Learn more about deploying Project Honolulu.](https://docs.microsoft.com/en-us/windows-server/manage/honolulu/deployment-guide)

#### Configure your gateway VM to enable HTTPS port access: 

1. Navigate to your VM in the Azure portal and select **Networking**. 

2. Select **Add inbound port rule** and select **HTTPS** under **Service**. 

> [!NOTE]
> If you chose a port other than the default 443, choose **Custom** under Service and enter the port you chose in step 3 under **Port ranges**. 

 

At this point, you should be able to access Project Honolulu from a modern browser (Edge or Chrome) on your local machine by navigating to the DNS name of your gateway VM. 

> [!NOTE]
> If you selected a port other than 443, you can access Project Honolulu by navigating to https://< DNS name of your VM >:< custom port >

In order to add other VMs in the VNet, ensure WinRM is running on the target VMs by running the following in PowerShell or the Command Prompt on the target VM: `winrm quickconfig`
