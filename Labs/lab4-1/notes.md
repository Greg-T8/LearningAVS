# Lab 4-1: Azure Arc - Onboarding a Server

**Objective:** Get your first Windows/Linux server onboarded into Azure Arc.

* Install the Connected Machine Agent on a VM (Windows + Linux if you can).
* Register the machine with your subscription and confirm it shows in the Azure Portal.
* Explore metadata: resource group, tags, location, identity.

## References

* [Quickstart: Connect a machine to Arc-enabled servers](https://learn.microsoft.com/en-us/azure/azure-arc/servers/quick-enable-hybrid-vm)

## Generate installation script

1. In the Azure portal, open the **Add servers (Azure Arc)** page. Select **Add a single server**, then select **Generate script**.

   **Tip:** You can also go to **Servers - Azure Arc** and select **+Add**.

    <img src='images/2025-10-01-03-23-30.png' width=900>

2. On **Basics**:
  a. Select the subscription and resource group for the machine.
  b. Choose the **Region** to store server metadata.
  c. Select the server **Operating system**.
  d. Under **Connectivity method**:
      * Choose **Public endpoint** or **Private endpoint**.
      * For **Private endpoint**, pick or create a private link scope.
      * To use a proxy, enter the URL in the format http://<proxyURL>:<proxyport>.
      * If using **Public endpoint** with **Azure Arc Gateway**, select or create a Gateway.
  e. Select **Next**.

3. On **Tags**:
   a. Review **Physical location** tags and enter values.
   b. Add any **Custom tags**.
   c. Select **Next**.

4. In **Download and run script**:
   a. Review the script; use **Previous** to adjust settings if needed.
   b. Select **Download** to save the script file.

Suggestions:
• Confirm required ports and firewall rules for your chosen connectivity method.
• Save the script with your CM tool (e.g., Intune, SCCM, Ansible) for consistent rollout.
• Store proxy and credentials in a secure secret store rather than hardcoding.
• Tag consistently (owner, environment, location) to simplify governance and cost reporting.
