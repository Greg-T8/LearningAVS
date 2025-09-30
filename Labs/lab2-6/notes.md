# Lab 2-6: Secure outbound internet connectivity for Azure VMware Solution

**Module:** [Secure outbound internet connectivity for Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/azure-vmware-solution/)


Learn how to route, control, and inspect outbound network traffic from an Azure VMware Solution private cloud. Define the outbound internet path using a custom or third-party Network Virtual Appliance (NVA) with Azure Route Server and Azure Firewall.

**Learning objectives:**

* Generate and inject a default route using a custom or third-party NVA, with Azure Firewall as the next hop.
* Propagate the default route to the Azure VMware Solution private cloud using Azure Route Server.
* Create network rules in Azure Firewall to allow outbound internet traffic from the private cloud.

**Prerequisites:**

* Beginner-level knowledge of Azure networking concepts: Virtual WAN, VNets, subnets, NSGs, route tables, UDR, ExpressRoute Gateway, and VNet peering.
* Intermediate experience with Azure VMware Solution private cloud, including NSX-T T0/T1 gateways, NSX-T Edge, and Managed SNAT.
* Ability to create a network segment, deploy a VM in it, and test internet connectivity.
* Intermediate experience with custom or third-party NVAs, BGP, Azure Route Server, and Azure Firewall.


<!-- omit in toc -->
## Contents

* [Introduction](#introduction)
  * [Example Scenario](#example-scenario)
  * [What will we be doing?](#what-will-we-be-doing)
  * [What is the main goal?](#what-is-the-main-goal)
* [Default outbound internet connectivity for Azure VMware Solution](#default-outbound-internet-connectivity-for-azure-vmware-solution)
  * [How workloads in Azure VMware Solution connect to the internet:](#how-workloads-in-azure-vmware-solution-connect-to-the-internet)
  * [Controlling internet traffic](#controlling-internet-traffic)
  * [Disable outbound internet connectivity](#disable-outbound-internet-connectivity)
* [Exercise - configure Azure VMware Solution](#exercise---configure-azure-vmware-solution)
  * [Knowledge check](#knowledge-check)
* [Exchange routes with Azure VMware Solution private cloud](#exchange-routes-with-azure-vmware-solution-private-cloud)
  * [Network paths in Azure VMware Solution private cloud](#network-paths-in-azure-vmware-solution-private-cloud)
  * [Network paths in Azure](#network-paths-in-azure)
  * [Enabling network path exchange between Azure VMware Solution and Azure](#enabling-network-path-exchange-between-azure-vmware-solution-and-azure)
* [Exercise - create and configure Azure Route Server](#exercise---create-and-configure-azure-route-server)
  * [Create Resource Group \& Virtual Network](#create-resource-group--virtual-network)
  * [Deploy Azure Route Server](#deploy-azure-route-server)
  * [Enable branch-to-branch connectivity](#enable-branch-to-branch-connectivity)
  * [Knowledge check](#knowledge-check-1)
* [Securing network communication](#securing-network-communication)
  * [Protecting digital assets](#protecting-digital-assets)
  * [Controlling network traffic](#controlling-network-traffic)
  * [Firewall internet route](#firewall-internet-route)
  * [Exercise - create and configure Azure Firewall](#exercise---create-and-configure-azure-firewall)
  * [Deploy Azure Firewall](#deploy-azure-firewall)
  * [Create route table and default route](#create-route-table-and-default-route)
  * [Associate the route table](#associate-the-route-table)
  * [Knwowledge check](#knwowledge-check)
* [Using FRRouting as an NVA](#using-frrouting-as-an-nva)
  * [Operational Processes](#operational-processes)
  * [Skills](#skills)
  * [Open-source](#open-source)
* [Exercise - configure custom router](#exercise---configure-custom-router)
  * [Configure FRR routing on the NVA](#configure-frr-routing-on-the-nva)
  * [Configure BGP neighbor and default route for the NVA](#configure-bgp-neighbor-and-default-route-for-the-nva)
  * [Configure Peering with Azure Route Server](#configure-peering-with-azure-route-server)
  * [Knowledge check](#knowledge-check-2)
* [Network Security Management](#network-security-management)
  * [Block by default](#block-by-default)
  * [Outbound network rules](#outbound-network-rules)
  * [Firewall rules for AVS](#firewall-rules-for-avs)
* [Exercise - establish outbound internet connectivity](#exercise---establish-outbound-internet-connectivity)
  * [Capture Azure VMware Solution workload segment address space](#capture-azure-vmware-solution-workload-segment-address-space)
  * [Configure Azure Firewall Rule for Azure VMware Solution workload network segment](#configure-azure-firewall-rule-for-azure-vmware-solution-workload-network-segment)
  * [Test outbound internet connectivity from a VM in Azure VMware Solution workload network segment](#test-outbound-internet-connectivity-from-a-vm-in-azure-vmware-solution-workload-network-segment)
  * [Knowledge check](#knowledge-check-3)
* [References](#references)

## Introduction

Organizations often face strict network traffic security requirements. In regulated industries like healthcare and finance, noncompliance can lead to costly penalties. This module shows how to secure outbound network traffic from Azure VMware Solution to meet those requirements.

### Example Scenario

Contoso, a healthcare customer, recently migrated its applications from an on-premises VMware environment to Azure VMware Solution.

The network security team wants to replicate the same traffic inspection and control processes in Azure that were used on-premises. This ensures compliance with strict healthcare regulations while supporting digital transformation efforts.

To achieve this, the team plans to control outbound traffic using a Network Virtual Appliance (NVA). All outbound traffic must be routed through a firewall, where rules will allow or deny traffic based on protocol, port, and IP address.

<img src='images/2025-09-29-03-26-10.png' width=900>

### What will we be doing?

You’ll set up connectivity between the Azure VMware Solution private cloud and Azure. A custom NVA will be deployed to generate the default route, with Azure Firewall as the next hop. Azure Route Server will propagate this route to the private cloud. Finally, Azure Firewall will manage the traffic through network rules.

### What is the main goal?

By the end of this module, you’ll know how to secure and manage outbound internet connectivity from the Azure VMware Solution private cloud using a custom NVA, Azure Route Server, and Azure Firewall.

## Default outbound internet connectivity for Azure VMware Solution

Azure VMware Solution enables running VMware workloads natively in Azure. You'll start with exploring current options available to VMware workloads for outbound internet connectivity.

### How workloads in Azure VMware Solution connect to the internet:

When you deploy an Azure VMware Solution private cloud, there are several options for outbound internet connectivity.

* If you use **Azure Virtual WAN**, you can inject the default route (0.0.0.0/0) to provide outbound internet access through Azure Firewall or a certified third-party NVA.
* If you don’t use Azure Virtual WAN, you can rely on the **Managed SNAT** capability built into Azure VMware Solution.
* If you need a fixed public IP for outbound traffic, you can assign a **public IP at the NSX Edge**.

The right choice depends on whether you already use services like Azure Virtual WAN and whether a fixed public IP is required for all outbound traffic.

### Controlling internet traffic

Contoso requires strict control over how workloads connect to the internet. These requirements ensure all internet-bound traffic from applications inside and outside Azure VMware Solution exits through a centralized point. To achieve this, Contoso plans to implement customized outbound connectivity on top of the built-in options provided by Azure VMware Solution.

### Disable outbound internet connectivity

To set up customized outbound connectivity, Contoso must first block all outbound internet access from the Azure VMware Solution private cloud. Instructions for disabling this connectivity are provided in the next unit.

## Exercise - configure Azure VMware Solution

Use this process to complete Contoso’s Azure VMware Solution configuration:

1. In the Azure portal, open your **Azure VMware Solution private cloud** and go to **Internet connectivity**. Select **Don’t connect or connect using the default route from Azure** (the default setting after deployment).

    <img src='images/2025-09-29-03-30-22.png' width=800>

2. Create an **NSX Manager network segment** in the Azure portal.

    <img src='images/2025-09-29-03-31-09.png' width=800>

3. Create a **DHCP server or DHCP relay** in the Azure portal.

    <img src='images/2025-09-29-03-31-27.png' width=700>

4. Configure a **DNS forwarder** under **Workload Networking** in the Azure portal.

    <img src='images/2025-09-29-03-31-53.png' width=800>

5. Deploy a VM as you would in any VMware vSphere environment.
6. Sign in to the VM and confirm it has no internet connectivity.

With this setup, the Azure VMware Solution private cloud is correctly configured, providing the foundation for securing outbound internet connectivity in the next unit.

### Knowledge check

<img src='images/2025-09-29-03-34-09.png' width=700>

## Exchange routes with Azure VMware Solution private cloud

Contoso runs a hybrid environment with an on-premises site, Azure cloud services, and an Azure VMware Solution private cloud. The next sections highlight key networking considerations specific to hybrid environments.

### Network paths in Azure VMware Solution private cloud

The private cloud includes a management segment that runs core services such as vSAN, NSX Data Center, and private cloud management. It also supports one or more workload segments for applications. Both management and workload segments use private IP address space. VMs in the same workload segment can communicate with each other, but additional configuration is needed for them to reach outside the private cloud. This is covered in the *Default outbound internet connectivity for Azure VMware Solution* unit.

### Network paths in Azure

Contoso connects its Azure VMware Solution private cloud to Azure using an ExpressRoute gateway. This gateway sits inside a hub VNet that uses private IP space. The hub VNet is also connected to the on-premises site via ExpressRoute and peered with other Azure VNets. Contoso plans to deploy its preferred NVA in the hub VNet.

### Enabling network path exchange between Azure VMware Solution and Azure

Contoso wants to exchange routes between multiple sources, including the on-premises environment, the ExpressRoute gateway, and the NVA. Since the IT team is experienced with Border Gateway Protocol (BGP), they plan to use it in Azure.

Azure enables BGP route exchange with **Azure Route Server (ARS)**. ARS can set the NVA’s private IPs as BGP peers, share its routes with the NVA, and learn routes from it. ARS also supports branch-to-branch connectivity with the ExpressRoute gateway, which links to Azure VMware Solution. This allows ARS to exchange routes with both management and workload segments of the private cloud.

You now have a foundation for understanding route exchange. The next unit will guide you through implementing it.

## Exercise - create and configure Azure Route Server

The following steps show how to create and configure Azure Route Server (ARS) using the Azure CLI. The same can be done using the Azure portal, PowerShell, or Terraform.

### Create Resource Group & Virtual Network

It’s recommended to deploy Azure VMware Solution as part of an **Azure Landing Zone Architecture**. In this setup, the AVS private cloud resides in a separate subscription, while networking services such as ARS, ExpressRoute Gateway, and Azure Firewall are deployed in a **Landing Zone connectivity subscription**.

Start by creating the required resource group, virtual network, subnet, and public IP:

```bash
az group create -l <your-preferred-azure-region> -n <resource-group-name>

az network vnet create \
  -n <vnet-name> \
  -g <resource-group-name> \
  --address-prefix 10.0.0.0/16

az network vnet subnet create \
  -n RouteServerSubnet \
  -g <resource-group-name> \
  --vnet-name <vnet-name> \
  --address-prefix 10.0.0.0/24

az network public-ip create \
  -n <name-for-route-server-pip> \
  -g <resource-group-name> \
  --version IPv4 \
  --sku Standard

$ars_subnet_id=$(az network vnet subnet show \
  --name RouteServerSubnet \
  --resource-group <resource-group-name> \
  --vnet-name <vnet-name> \
  --query id -o tsv)
```

### Deploy Azure Route Server

Azure Route Server uses BGP to exchange routes between an NVA and the ExpressRoute Gateway that connects Azure VMware Solution. Deploy ARS with:

```bash
az network routeserver create \
  --name <routeserver-name> \
  --resource-group <resource-group-name> \
  --hosted-subnet $ars_subnet_id \
  --public-ip-address <name-for-route-server-pip>
```

### Enable branch-to-branch connectivity

Finally, enable branch-to-branch traffic to complete the route exchange setup:

```bash
az network routeserver update \
  --name <routeserver-name> \
  --resource-group <resource-group-name> \
  --allow-b2b-traffic true
```

You’ve now completed two key tasks:

1. Configured Azure VMware Solution private cloud for outbound internet connectivity.
2. Set up a route exchange mechanism between ARS and Azure VMware Solution.

In the next unit, you’ll add security into this design.

### Knowledge check

<img src='images/2025-09-29-03-52-55.png' width=700>

## Securing network communication

As a healthcare industry leader, Contoso must operate under strict regulatory compliance. Here are key considerations for securing network communication:

### Protecting digital assets

Every VM in Azure and Azure VMware Solution must be protected. All inbound and outbound traffic must be inspected for malicious activity in real time. Contoso also wants IT administrators to control access to potentially risky websites, such as certain social media platforms.

### Controlling network traffic

Contoso runs multiple VNets, each with several subnets. They need clear rules to define which traffic is allowed between subnets. This control ensures only approved traffic flows and overrides Azure’s default policy, which allows subnet-to-subnet communication by default.

### Firewall internet route

After reviewing requirements, Contoso chose **Azure Firewall**, a managed, stateful firewall service. It filters traffic across hybrid networks using ExpressRoute and VPN gateways, making it suitable for Azure VMware Solution. For outbound access, Azure Firewall requires direct internet connectivity, which is enabled by configuring rules on the subnet where it’s deployed.

The next unit provides technical instructions to implement Azure Firewall and enforce these traffic controls, helping meet Contoso’s compliance and security needs.

### Exercise - create and configure Azure Firewall

> Try the **Secure Outbound Internet Connectivity** click-through demo. It demonstrates how to use an Azure VNet with Route Server, a Network Virtual Appliance (NVA), and Azure Firewall to inspect and manage traffic from Azure VMware Solution to the internet. [Click-through demo link](https://regale.cloud/microsoft/play/4174/secure-outbound-internet-connectivity#/0/0)

### Deploy Azure Firewall

Create the AzureFirewallSubnet and a public IP.

```bash
az network vnet subnet create \
  -n AzureFirewallSubnet \
  -g <resource-group-name> \
  --vnet-name <vnet-name> \
  --address-prefix 10.0.1.0/24

az network public-ip create \
  -n <name-for-firewall-pip> \
  -g <resource-group-name> \
  --version IPv4 \
  --sku Standard
```

Create the firewall and its IP configuration.

```bash
az network firewall create \
  -n <name-of-firewall> \
  -g <resource-group-name> \
  --location <your-preferred-azure-region>

az network firewall ip-config create \
  --firewall-name <name-of-firewall> \
  --name <firewall-config-name> \
  --public-ip-address <name-of-firewall-pip> \
  --resource-group <resource-group-name> \
  --vnet-name <vnet-name>

az network firewall update \
  --name <name-of-firewall> \
  --resource-group <resource-group-name>
```

### Create route table and default route

Create a route table with BGP propagation disabled.

```bash
az network route-table create \
  --name <firewall-route-table-name> \
  --resource-group <resource-group-name> \
  --location <your-preferred-azure-region> \
  --disable-bgp-route-propagation true
```

Add a 0.0.0.0/0 route to the internet for firewall egress.

```bash
az network route-table route create \
  --resource-group <resource-group-name> \
  --name <route-name> \
  --route-table-name <firewall-route-table-name> \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type Internet
```

### Associate the route table

Associate the route table to the AzureFirewallSubnet.

```bash
az network vnet subnet update \
  --name AzureFirewallSubnet \
  --resource-group <resource-group-name> \
  --vnet-name <vnet-name> \
  --route-table <firewall-route-table-name>
```

### Knwowledge check

<img src='images/2025-09-29-04-03-03.png' width=700>

## Using FRRouting as an NVA

You will continue using Network Virtual Appliances (NVAs) to generate the default route, just as in your on-premises setup. This section outlines the key factors to consider when maintaining this approach.

### Operational Processes

NVAs are part of Contoso's Standard Operating Procedures. Monitoring and management depend on NVA features like traffic rules and network segmentation. Using the same processes and features simplifies migration from on-premises to Azure.

### Skills

Contoso's IT team has extensive experience with NVAs. Since NVAs can be deployed and managed in Azure, the team can reuse existing skills instead of learning a new Azure service.

### Open-source

NVAs can be proprietary or open source. Proprietary NVAs involve extra costs but come with vendor support. Open-source NVAs are community-driven, provide source code access, and rely on community support. At Contoso, an open-source NVA is used, offering flexibility with open protocols like BGP. NVAs also integrate easily with VMs and Azure networks while supporting default route generation.

Contoso relies on FRRouting (FRR), a free, open-source routing implementation, for these reasons.

In the next unit, you will configure FRR in Azure to generate the default route.

## Exercise - configure custom router

Use the following instructions to configure the infrastructure for FRR and generate the default route:

```bash
az network vnet subnet create \
    -n <NVA-Subnet-name> \
    -g <resource-group-name> \
    --vnet-name <vnet-name> \
    --address-prefix 10.0.2.0/24

az network nic create \
    -g <resource-group-name> \
    --vnet-name <vnet-name> \
    --subnet <NVA-Subnet-name> \
    -n <NVA-nic-name>

az vm availability-set create \
    --name <nva-availability-set-name> \
    --resource-group <resource-group-name> \
    --location <your-preferred-azure-region>

az vm create \
    --name <nva-vm-name> \
    --resource-group <resource-group-name> \
    --location <your-preferred-azure-region> \
    --image OpenLogic:CentOS:8_5:8.5.2022012100 \
    --size Standard_D1_v2 \
    --availability-set <nva-availability-set-name> \
    --authentication-type password \
    --admin-username <vm-admin-user-name> \
    --admin-password <vm-admin-username-password> \
    --storage-sku Standard_LRS \
    --nics <NVA-nic-name>
```

### Configure FRR routing on the NVA

Now, you're going to configure the FRR software.

1. Update the routeServerSubnetPrefix and bgpNvaSubnetGateway variables in the following script.

    ```bash
    # IP prefix of the RouteServerSubnet in the Firewall VNet.
    routeServerSubnetPrefix="<azure-route-server-subnet-prefix>"

    # First IP of the subnet attached to eth0 (gateway).
    bgpNvaSubnetGateway="<nva-azure-subnet-first-ip-address>"

    # Install FRR
    sudo dnf install frr -y

    # Enable bgpd and prepare config
    sudo sed -i 's/bgpd=no/bgpd=yes/g' /etc/frr/daemons
    sudo touch /etc/frr/bgpd.conf
    sudo chown frr /etc/frr/bgpd.conf
    sudo chmod 640 /etc/frr/bgpd.conf

    # Start FRR
    sudo systemctl enable frr --now

    # Add static route for Azure Route Server reachability (transient)
    sudo ip route add $routeServerSubnetPrefix via $bgpNvaSubnetGateway dev eth0

    # To persist on CentOS, add to /etc/sysconfig/network-scripts/route-eth0
    # e.g.,: <azure-route-server-subnet-prefix> via <nva-azure-subnet-first-ip-address> dev eth0
    ```

2. Copy the edited script.

3. Sign in to the NVA VM shell.

4. Paste the script as plain text (Ctrl-Shift-V) and run it.

5. Confirm no errors appear at completion. Capture a screenshot showing success.

      <img src='images/2025-09-30-02-58-54.png' width=800>

6. Run **sudo vtysh**.

7. Verify the FRR shell starts. Capture a screenshot showing the sudo vtysh execution.

      <img src='images/2025-09-30-02-59-30.png' width=400>

### Configure BGP neighbor and default route for the NVA

1. Configure the FRR NVA to peer with Azure Route Server and advertise a default route. Update these variables first:

    1. \<Firewall Private IP address\>
    2. \<IP address of Route Server instance #0\>
    3. \<IP address of Route Server instance #1\>

    ```bash
    conf term
    !
    route-map SET-NEXT-HOP-FW permit 10
     set ip next-hop <Firewall Private IP address>
    exit
    !
    router bgp 65111
     no bgp ebgp-requires-policy
     neighbor <IP address of Route Server instance #0> remote-as 65515
     neighbor <IP address of Route Server instance #0> ebgp-multihop 2
     neighbor <IP address of Route Server instance #1> remote-as 65515
     neighbor <IP address of Route Server instance #1> ebgp-multihop 2
     network 0.0.0.0/0
    !
    address-family ipv4 unicast
     neighbor <IP address of Route Server instance #0> route-map SET-NEXT-HOP-FW out
     neighbor <IP address of Route Server instance #1> route-map SET-NEXT-HOP-FW out
    exit-address-family
    !
    exit
    !
    exit
    !
    write file
    !
    ```

2. Sign in to the FRR shell (sudo vtysh).
3. Paste the script with updated variables and run it.
4. Run `show ip bgp` and confirm only the default route is present.
5. Run `show ip bgp sum` and confirm no BGP sessions are established.

    <img src='images/2025-09-30-03-03-50.png' width=600>

### Configure Peering with Azure Route Server

1. Create BGP peering with Azure Route Server.

    ```bash
    az network routeserver peering create \
      --name <nva-vm-name> \
      --peer-ip <private-ip-of-nva-vm-name> \
      --peer-asn <asn-value-other-than-65515-65520> \
      --routeserver <routeserver-name> \
      --resource-group <resource-group-name>
    ```

2. Enable branch-to-branch on Route Server.

    ```bash
    az network routeserver update \
      --name <routeserver-name> \
      --resource-group <resource-group-name> \
      --allow-b2b-traffic true
    ```

3. Sign in to FRR shell.

4. Run `show ip bgp` and confirm routes are learned from Azure Route Server. Azure Route Server should appear as next hop for AVS networks.

    <img src='images/2025-09-30-03-05-40.png' width=600>

5. In **Azure portal**, open the **Route Table** linked to the Azure Firewall subnet. Verify the **ToInternet** route points to **Internet** as next hop.

    <img src='images/2025-09-30-03-06-09.png' width=400>

At this point, AVS outbound traffic is secured via Azure Firewall. Azure Route Server exchanges routes with the NVA, and FRR injects the default route with the firewall as next hop.

### Knowledge check

<img src='images/2025-09-30-03-10-31.png' width=700>

## Network Security Management

In this unit, you configure fine-grained rules that allow Azure VMware Solution (AVS) private cloud to access the internet.

### Block by default

Azure Firewall follows a "block by default" design. Any traffic routed through it is blocked unless explicitly allowed. You have already routed AVS traffic through Azure Firewall, but no traffic is permitted yet. This principle forms the basis for applying precise network rules.

### Outbound network rules

Some traffic must bypass the default block. Azure Firewall supports two approaches:

* **Classic rules:** Define rules per firewall instance using protocol, source, destination, and port. Suitable for smaller environments, but not scalable across multiple firewalls.
* **Firewall policy:** Define rules once and apply them to multiple instances. Recommended for enterprise deployments due to easier management and scalability.

### Firewall rules for AVS

In this unit, you will use classic rules instead of firewall policies. Rules for AVS include workload segment IP ranges, protocol, and port. Set **Destination type** to **IP Address**. For **Destination address space**, use `*`. For **Destination ports**, specify `*` or limit to required ports such as 80 or 443.

**Screenshot:** Azure Firewall network rule with "IP Addresses" highlighted for source and destination fields.

<img src='images/2025-09-30-03-13-29.png' width=600>

## Exercise - establish outbound internet connectivity

This unit covers implementing network controls with Azure Firewall and testing them from a VM in the Azure VMware Solution (AVS) workload segment.

### Capture Azure VMware Solution workload segment address space

Use the following command to get the workload segment address space from Azure VMware Solution private cloud:

```bash
az vmware workload-network segment show \
  --resource-group <resource-group-name> \
  --private-cloud <avs-private-cloud-name>
```

### Configure Azure Firewall Rule for Azure VMware Solution workload network segment

Use the following command to configure Firewall rule for workload segment:

```bash
az network firewall network-rule create \
  --collection-name <firewall-rule-collection-name> \
  --destination-addresses <*-or-specific-addresses> \
  --destination-ports <*-or-specific-ports> \
  --firewall-name <name-of-firewall> \
  --name <firewall-rule-name> \
  --protocols <*-or-specific-protocols> \
  --resource-group <resource-group-name> \
  --priority <priority-value> \
  --source-addresses <avs-workload-segment-address-space> \
  --action Allow
```

### Test outbound internet connectivity from a VM in Azure VMware Solution workload network segment

1. Sign in the VM in Azure VMware Solution workload network segment.
2. Navigate to any public URL from the VM. Any public URL should be accessible from browser.

### Knowledge check

<img src='images/2025-09-30-03-16-08.png' width=700>

## References

* [Azure Landing Zone Architecture](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/azure-vmware/enterprise-scale-landing-zone#architecture)
* [Azure Landing Zone connectivity subscription](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org-subscriptions)
* [Border Gateway Protocol](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)
* [FRRouting](https://frrouting.org/)
* [AVS Landing Zone Accelerator - Reference Architecture](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/azure-vmware/enterprise-scale-landing-zone)
* [AVS Landing Zone Accelerator - Reference Implementation](https://github.com/Azure/Enterprise-Scale-for-AVS)
