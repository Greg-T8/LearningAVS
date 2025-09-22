# Lab 1: Introduction to Azure VMware Solution (AVS)



**Module:** [Introduction to Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/intro-azure-vmware-solution/)

Learn how Azure VMware Solution helps enterprises move to the cloud. Review its features and unique advantages. Explore common use cases and customer examples. Understand the available support options. Get an overview of the service components.

**Learning objectives:**  
By the end of this module, you will be able to:

* Assess whether Azure VMware Solution is a good fit for current business challenges.
* Identify the main differences between Azure VMware Solution and on-premises VMware.
* Explain how the service affects existing licensing when using Azure Hybrid Benefit.
* Describe the core components of the service.

<!-- omit in toc -->
## Contents

- [Introduction](#introduction)
- [What is Azure VMware Solution?](#what-is-azure-vmware-solution)
  - [Key features](#key-features)
  - [Integration with Azure services](#integration-with-azure-services)

## Introduction

Azure VMware Solution is an Azure service that provides a vSphere-based, single-tenant private cloud. It runs on dedicated bare-metal infrastructure in Azure.

With this service, you can run, manage, and secure applications across VMware environments and Azure using a consistent operating model.

**Example scenario**  
You work for a healthcare company that hosts many production workloads on VMware vSphere across multiple offices.

Recently, several challenges have surfaced. Some locations have hardware nearing end of life, requiring urgent refresh decisions. At the same time, the company struggles to acquire new infrastructure quickly enough to meet refresh and application demands. The requisition process, approvals, and logistics can take four to six months or more.

To address these issues, the company is exploring migration of VMware vSphere workloads to the cloud, without changing the virtualization platform or altering current operations.

What will you learn?
In this module, you’ll explore:

* The business value of Azure VMware Solution
* Common use cases driving adoption
* Core service components
* Connectivity options between datacenters
* Licensing benefits with Azure Hybrid Benefit

**Main goal**  
By the end of this module, you will be able to evaluate whether Azure VMware Solution is the right choice for your company’s cloud strategy.

## What is Azure VMware Solution?

Every company is at a different stage in its digital transformation and cloud adoption. To support this, Microsoft and VMware developed Azure VMware Solution. This service lets organizations move VMware-based workloads from on-premises to Azure, deploy new virtual machines, and integrate workloads with other Azure services.

Azure VMware Solution combines VMware’s Software-Defined Data Center (SDDC) with the global scale of Azure. It’s VMware-validated and continuously certified through testing of upgrades and enhancements.

Microsoft manages the service to meet enterprise requirements for performance, availability, security, and compliance. The solution includes:

* Management systems
* Networking services
* Operating platform
* Back-end infrastructure operations

VMware vSphere clusters run on bare-metal Azure hosts and can be deployed and managed through the Azure portal, Azure CLI, or PowerShell. Each private cloud comes with supported versions of VMware vCenter Server, vSAN, vSphere, and NSX—fully licensed. The service also provides elasticity, allowing you to scale compute, network, and storage resources up or down as needed.

The following diagram shows how private clouds, Azure services, and on-premises environments connect within Azure.

<img src='images/2025-09-22-03-20-39.png' width=650>

### Key features

Azure VMware Solution delivers a complete VMware environment in Azure, allowing your organization to:

* Use existing VMware investments: Continue using familiar VMware vSphere and vCenter tools without retraining staff or refactoring hypervisors.
* Maintain operational consistency for admins: Manage Azure-based VMware resources with standard interfaces like vSphere Client and NSX Manager. The Azure portal is used only for deployment and management.
* Preserve business operations: Reduce change management by leveraging existing training, processes, tools, and practices. Use VMware HCX Enterprise for large-scale, live migrations from on-premises to Azure with minimal downtime.
* Rely on a single point of support: Microsoft develops, operates, and supports the service, coordinating directly with VMware when needed.
* Simplify licensing: Manage licensing and resource consumption through one vendor, with VMware product licenses tied directly to deployed resource nodes.
* Maximize existing licenses: Apply Azure Hybrid Benefit for Windows Server and SQL Server to bring current licenses to Azure and lower costs.

### Integration with Azure services

Azure VMware Solution lets customers migrate their VMware vSphere environments to the cloud at their own pace. Administrators manage everything through the Azure portal without relying on third-party services and can gradually build their cloud skills while working with Azure.

Native Azure tools and services can monitor and manage virtual machines across environments. These same tools work with VMs in Azure VMware Solution, standard Azure VMs, and on-premises VMs.

<img src='images/2025-09-22-03-24-48.png' width=500>  

| Integration                  | Description                                                                                               |
| ---------------------------- | --------------------------------------------------------------------------------------------------------- |
| Log Analytics workspace      | Dedicated environment in Azure Monitor to store and manage log data for configured sources and solutions. |
| Microsoft Defender for Cloud | Unified security management with advanced threat protection for hybrid workloads.                         |
| Microsoft Sentinel           | Cloud-native SIEM providing analytics, alerts, and automated threat response.                             |
| Azure NetApp Files           | Mountable SMB/NFS shares for Linux and Windows clients in Azure VMware Solution.                          |
| Azure Backup Server          | Backup VMware VMs on ESXi or vCenter to Azure.                                                            |
| Azure Application Gateway    | Layer-7 load balancer to secure and manage web app traffic.                                               |
| Azure Traffic Manager        | Distributes workloads across multiple endpoints for availability and performance.                         |
