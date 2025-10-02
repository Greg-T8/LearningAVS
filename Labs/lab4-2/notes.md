# Lab 4.2 - Azure Arc - Azure Policy and Guest Configuration

**Objective:** Apply governance controls.

1. Assign a built-in Azure Policy (e.g., “Audit if Log Analytics agent is not installed”).
2. Onboard the server into Azure Automanage Machine Configuration (formerly known as _Guest Configuration_).
3. Validate compliance reporting in the Portal.

<!-- omit in toc -->
## Contents

* [Objective 1: Assign a built-in Azure Policy](#objective-1-assign-a-built-in-azure-policy)
  * [Create a policy assignment](#create-a-policy-assignment)
  * [Identify non-compliant resources](#identify-non-compliant-resources)
* [Objective 2: Onboard the server into Azure Automanage Machine Configuration](#objective-2-onboard-the-server-into-azure-automanage-machine-configuration)

## Objective 1: Assign a built-in Azure Policy

* [Assign policy to assess compliance](https://learn.microsoft.com/en-us/azure/azure-arc/servers/tutorial-assign-policy-portal)

### Create a policy assignment

1. Open the Azure portal and search for **Policy** to launch the Azure Policy service.
2. In the left menu under **Authoring**, select **Assignments**.
3. At the top of the Assignments pane, select **Assign Policy**.

    <img src='images/2025-10-02-04-00-25.png' width=800>

4. On the **Assign Policy** page, set the **Scope** by selecting the ellipsis. Choose a management group or subscription, and optionally a resource group. Select **Select** when done.
5. Leave **Exclusions** blank unless you need to exclude resources.
6. Select the **Policy definition** ellipsis to open the list of definitions. Search for **Azure Defender for servers should be enabled**. Select it and choose **Add**.

      <img src='images/2025-10-02-04-03-16.png' width=800>

7. The **Assignment name** will auto-fill with the policy name. Keep the default and leave other options unchanged.
8. Skip the additional tabs. Select **Review + Create**, then select **Create**.
9. The policy is now assigned. You can check for noncompliant resources to understand your environment’s compliance state.

### Identify non-compliant resources

1. In the Azure portal, open **Policy**.
2. From the left menu, select **Compliance**.
3. Locate the **Azure Defender for servers should be enabled** policy assignment.
4. Any resources not meeting the assignment will show **Non-compliant** under Compliance state.

      <img src='images/2025-10-02-04-12-42.png' width=800>

Compliance is determined by evaluating conditions against resources:

| Resource state | Effect                                                    | Policy evaluation | Compliance state |
| -------------- | --------------------------------------------------------- | ----------------- | ---------------- |
| Exists         | Deny, Audit, Append*, DeployIfNotExist*, AuditIfNotExist* | True              | Non-compliant    |
| Exists         | Deny, Audit, Append*, DeployIfNotExist*, AuditIfNotExist* | False             | Compliant        |
| New            | Audit, AuditIfNotExist*                                   | True              | Non-compliant    |
| New            | Audit, AuditIfNotExist*                                   | False             | Compliant        |

*Append, DeployIfNotExist, and AuditIfNotExist require the IF condition to be **true** and the existence condition to be **false** for a resource to be marked noncompliant.*

<img src='images/2025-10-02-04-15-50.png' width=500>

## Objective 2: Onboard the server into Azure Automanage Machine Configuration

- [Understanding Azure Machine Configuration](https://learn.microsoft.com/en-us/azure/governance/machine-configuration/overview#deploy-requirements-for-azure-virtual-machines)

Azure Policy’s machine configuration feature lets you audit and configure operating system settings as code on both Azure machines and Arc-enabled hybrid machines. You can apply configurations per machine or manage them at scale through Azure Policy.

Configuration resources act as extension resources, adding extra properties to a machine. These configurations can cover:

* Operating system settings
* Application configuration or presence
* Environment settings

Machine configurations differ from policy definitions. While Azure Policy is used to assign them dynamically, configurations can also be applied manually.

**Scenarios:**  

| Type                     | Description                                                                                                                                    | Example story                                                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Configuration management | Full server representation as code in source control. Deployment includes server properties (size, network, storage) and OS/app configuration. | "This machine should be a web server configured to host my website."                                                                         |
| Compliance               | Audit or deploy settings across machines. Apply reactively to existing ones or proactively to new ones as they’re deployed.                    | "All machines should use TLS 1.2. Audit existing machines to release changes at scale. For new machines, enforce the setting on deployment." |

Per [Deploy requirements for Azure virtual machines](https://learn.microsoft.com/en-us/azure/governance/machine-configuration/overview#deploy-requirements-for-azure-virtual-machines), this extension is already included in the Arc Connected Machine agent.

<img src='images/2025-10-02-04-39-40.png' width=600>