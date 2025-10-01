# Lab 4-1: Azure Arc - Onboarding a Server

**Objective:** Get your first Windows/Linux server onboarded into Azure Arc.

* Install the Connected Machine Agent on a VM (Windows + Linux if you can).
* Register the machine with your subscription and confirm it shows in the Azure Portal.
* Explore metadata: resource group, tags, location, identity.

<!-- omit in toc -->
## Contents

* [References](#references)
* [Generate installation script](#generate-installation-script)
* [Install the agent using the script](#install-the-agent-using-the-script)
  * [Windows agent](#windows-agent)
  * [Linux agent](#linux-agent)
* [Verify connection with Azure Arc](#verify-connection-with-azure-arc)
* [Troubleshooting](#troubleshooting)
  * [Automating -The quick path](#automating--the-quick-path)
  * [Automating at scale](#automating-at-scale)
  * [Notes \& tips](#notes--tips)

## References

* [Quickstart: Connect a machine to Arc-enabled servers](https://learn.microsoft.com/en-us/azure/azure-arc/servers/quick-enable-hybrid-vm)

## Generate installation script

1. In the Azure portal, open the **Add servers (Azure Arc)** page. Select **Add a single server**, then select **Generate script**.

   **Tip:** You can also go to **Servers - Azure Arc** and select **+Add**.

    <img src='images/2025-10-01-03-23-30.png' width=700>

2. On **Basics**:
    * a. Select the subscription and resource group for the machine.
    * b. Choose the **Region** to store server metadata.
    * c. Select the server **Operating system**.
    * d. Under **Connectivity method**:
        * Choose **Public endpoint** or **Private endpoint**.
        * For **Private endpoint**, pick or create a private link scope.
        * To use a proxy, enter the URL in the format http://<proxyURL>:<proxyport>.
        * If using **Public endpoint** with **Azure Arc Gateway**, select or create a Gateway.
    * e. Select **Next**.
    <br><br>
    <img src='images/2025-10-01-03-43-51.png' width=800>

3. On **Tags**:
   * a. Review **Physical location** tags and enter values.
   * b. Add any **Custom tags**.
   * c. Select **Next**.
  <br><br>
   <img src='images/2025-10-01-03-45-55.png' width=400>

4. In **Download and run script**:
   * a. Review the script; use **Previous** to adjust settings if needed.
   * b. Select **Download** to save the script file.

    See [OnboardingScript.ps1](./scripts/OnboardingScript.ps1)

## Install the agent using the script

1. Copy the downloaded script to the target server.

2. Run the script with administrative privileges.

3. The script will:
   • Download the Connected Machine agent from Microsoft Download Center.
   • Install the agent on the server.
   • Create the Azure Arc–enabled server resource.
   • Link the resource with the agent.

4. Follow the instructions specific to your server’s operating system to complete the onboarding process.

### Windows agent

1. Log in to the Windows server.

2. Open a 64-bit PowerShell command prompt with administrator privileges.

3. Navigate to the folder or network share containing the script.

4. Run the script by executing:

```powershell
./OnboardingScript.ps1
```

**Output:**

<img src='images/2025-10-01-03-55-32.png' width=700>

**Note:** The script does prompt for interactive authentication

<img src='images/2025-10-01-03-55-55.png' width=500>

### Linux agent

1. Log in to the Linux server.

2. Run the installation script depending on your connectivity setup:

* For servers with direct access to Azure:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

* For servers using a proxy:

    ```bash
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

**Suggestion:** Make the script executable first if needed by running `chmod +x ~/Install_linux_azcmagent.sh`. Also verify that outbound connectivity to Azure endpoints is allowed (directly or via proxy).

## Verify connection with Azure Arc

1. Open the Azure portal and go to the **Hybrid machines** page.
   Tip: You can also search for **Machines - Azure Arc** in the portal.

2. Locate your server in the list.

3. Confirm that the machine shows a **Connected** status.

   <img src='images/2025-10-01-04-00-13.png' width=800>

**Suggestion:** If the status is not connected, check agent logs on the server, verify network connectivity, and confirm that the correct subscription and resource group were selected during onboarding.

## Troubleshooting

The script runs the following command which connects the machine to Azure Arc. This command requires an interactive login.

```pwsh
& "$env:ProgramW6432\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --resource-group "$env:RESOURCE_GROUP" `
  --tenant-id "$env:TENANT_ID" `
  --location "$env:LOCATION" `
  --subscription-id "$env:SUBSCRIPTION_ID" `
  --cloud "$env:CLOUD" `
  --gateway-id "$env:GATEWAY_ID" `
  --tags 'Datacenter=GregsHome,City=Austin,StateOrDistrict=TX,CountryOrRegion=USA' `
  --correlation-id "$env:CORRELATION_ID";
```

Use options with the `connect` subcommand to automate onboarding without interactive login.

<img src='images/2025-10-01-04-23-00.png' width=800>

See the following options for automating this process:

Use a service principal so `azcmagent.exe connect` can run **non-interactively** (no browser/device-code prompt).

### Automating -The quick path

1. **Create a least-privileged SP**

    * In Azure, create a service principal and grant it **Azure Connected Machine Onboarding** at the *subscription* or (better) the target *resource group* scope. 

2. **Run `azcmagent.exe connect` with SP credentials**

    ```powershell
    # Secret-based (simple)
    azcmagent.exe connect `
      --subscription-id "<sub-id>" `
      --resource-group "<rg-name>" `
      --location "<azure-region>" `
      --service-principal-id "<appId>" `
      --service-principal-secret "<secret>" `
      --tenant-id "<tenant-id>"
    ```

or (preferred) **certificate-based**:

  ```powershell
  azcmagent.exe connect `
    --subscription-id "<sub-id>" `
    --resource-group "<rg-name>" `
    --location "<azure-region>" `
    --service-principal-id "<appId>" `
    --service-principal-cert "C:\path\sp-cert.pem" `
    --tenant-id "<tenant-id>"
  ```

(Password-protected PFX/PEM aren’t supported by the agent.)

3. **Avoid putting secrets on the command line**

   Place inputs in a small config file and pass `--config`:

    ```json
    {
      "subscription-id": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
      "resource-group": "Arc-Hybrid",
      "location": "eastus",
      "service-principal-id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "service-principal-secret": "YOUR_SECRET",
      "tenant-id": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"
    }
    ```

```powershell
azcmagent.exe connect --config C:\arc\connect.json
```

(The CLI supports config files to keep secrets out of console logs.) 

### Automating at scale

* **Portal-generated script** (downloads agent and connects with your parameters) — good template for GPO/SCCM/Intune.
* **Bulk onboarding with SP** (Microsoft’s “at scale” doc).

### Notes & tips

* You can also pass a short-lived **access token** with `--access-token` if your automation already obtains one (e.g., `Get-AzAccessToken`).
* The onboarding credential is **only** needed during `connect`; once connected, the server stays Arc-registered even if that SP secret/cert expires—so rotate it freely. ([Microsoft Learn][5])
