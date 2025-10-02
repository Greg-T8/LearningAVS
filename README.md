# Learning Azure VMware Solution (AVS)

## Overview

This is my learning space for Azure VMware Solution (AVS). I intend to use this space to document my learning journey as I quickly ramp up on AVS.

AVS is a first-party Microsoft service that allows you to run a fully managed VMware software-defined data center (SDDC) on Azure infrastructure.

With AVS, organizations can:

- Save money from expensive Broadcom renewals by migrating VMware workloads to Azure (update: per [Microsoft Azure Update](https://azure.microsoft.com/en-us/updates?id=503878), Broadcom now requires customers to have an active VCF subscription)
- Migrate VMware workloads from on-premises to Azure with minimal changes.
- Extend their datacenter capacity on demand.
- Integrate VMware workloads with native Azure services (storage, networking, security, monitoring, and more).

## Azure VMware Solution - Foundation

This learning path is designed to provide a foundational understanding of Azure VMware Solution (AVS): what it is, when to use it, and how it fits into a broader cloud strategy. It equips team members with the core concepts, terminology, and business drivers behind AVS, while aligning to Microsoft’s Cloud Adoption Framework.

- [x] [Lab 1-1: Introduction to Azure VMware Solution](./Labs/lab1-1/notes.md) - completed 9/22/25
- [x] [Lab 1-2: Introduction to Azure Migrate for server migration](./Labs/lab1-2/notes.md) (1hr) - completed 9/23/25
- [x] [Lab 1-3: Introduction to the Microsoft Cloud Adoption Framework](./Labs/lab1-3/notes.md) (2hrs) - completed 9/25/25

## Azure VMware Solution - Engineer

This learning path is designed to equip Azure engineers with the skills needed to deploy, operate, and support Azure VMware Solution (AVS) environments. Building on the Azure VMware Solution – Foundation path, it focuses on tasks such as AVS provisioning, network integration with ExpressRoute, NSX-T configuration, and executing workload migrations using VMware HCX.

- [x] [Lab 2-1: Prepare to migrate VMWare resources to Azure by deploying Azure VMware Solution](./Labs/lab2-1/notes.md) (1hr) - added 9/23/25 - completed 9/27/25
- [x] [Lab 2-2: Migrate VMware vSphere resources from on-premises to Azure VMware Solution](./Labs/lab2-2/notes.md) (1hr) - completed 9/29/25
- [ ] [Lab 2-3: Set up Azure Migrate for server migration](https://learn.microsoft.com/en-us/training/modules/m365-azure-migrate-set-up/) (0.5hrs)
- [ ] [Lab 2-4: Design network solutions](https://learn.microsoft.com/en-us/training/modules/design-network-solutions/) (1hr)
- [ ] [Lab 2-5: Understand Azure NetApp Files essentials](./Labs/lab2-5/notes.md) (1hr) - in progress
- [x] [Lab 2-6: Secure outbound internet connectivity for Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/azure-vmware-solution/) (1.5hrs) - completed 9/30/25

## Azure VMware Solution - Architect

This learning path is designed to prepare architects to lead the end-to-end design and strategic adoption of Azure VMware Solution (AVS). It builds upon Azure VMWare Solution - Engineer knowledge with advanced topics such as secure landing zone architecture, multi-region and multi-subscription design, and integration with enterprise governance frameworks. Architects will learn how to align AVS deployments with business continuity, modernization, and cost optimization goals—positioning AVS as a key pillar in enterprise cloud strategy.

- [ ] [Lab 3-2: Using Azure NetApp Files with Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/azure-netapp-files-with-vmware-solution/) (0.5hrs)
- [ ] [Lab 3-2: AZ-700: Design and Implement Azure Network Solutions](https://learn.microsoft.com/en-us/training/paths/design-implement-microsoft-azure-networking-solutions-az-700/) (7 hrs)

## Azure Arc for Servers

This is a custom learning path I created to quickly ramp up on Azure Arc, specifically Azure Arc for Servers.

- [x] [Lab 4-1: Onboarding a Server](./Labs/lab4-1/notes.md) - completed 10/1/25
- [x] [Lab 4-2: Azure Policy and Guest Configuration](.labs/lab4-2/notes.md) - completed 10/2/25
- [ ] [Lab 4-3: Security with Defender for Cloud](tbd)
- [ ] [Lab 4-4: Monitoring and Log Analytics](tbd)
- [ ] [Lab 4-5: Update Management and Inventory](tbd)
- [ ] [Lab 4-6: Extensions](tbd)
- [ ] [Lab 4-7: At-Scale Scenarios](tbd)

## References

- [Azure VMware Solution Documentation](https://learn.microsoft.com/en-us/azure/azure-vmware/)
- [Microsoft Learning Path: Run VMware resources on Azure VMware Solution](https://learn.microsoft.com/en-us/training/paths/run-vmware-workloads-azure-vmware-solution/)
  - [Microsoft Learn Module: Introduction to Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/intro-azure-vmware-solution/)
  - [Microsoft Learn Module: Prepare to migrate VMWare resources to Azure by deploying Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/deploy-azure-vmware-solution/)
  - [Microsoft Learn Module: Migrate VMware vSphere resources from on-premises to Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/migrate-vmware-workloads-on-premises-azure-vmware-solution/)
  - [Microsoft Learn Module: Secure outbound internet connectivity for Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/azure-vmware-solution/)
  - [Microsoft Learn Module: Using Azure NetApp Files with Azure VMware Solution](https://learn.microsoft.com/en-us/training/modules/azure-netapp-files-with-vmware-solution/)
  - [Microsoft Learn Module: Run VMware resources on Azure VMware Solution](https://learn.microsoft.com/en-us/training/paths/run-vmware-workloads-azure-vmware-solution/)
- [VMWare Course Catalog - Azure VMware Solution - Ligtning Lab](https://labs.hol.vmware.com/HOL/catalog/lab/14620)
- [VMware Course Catalog - Azure VMware Solution Planning and Deployment](https://labs.hol.vmware.com/HOL/catalog/lab/14619)
