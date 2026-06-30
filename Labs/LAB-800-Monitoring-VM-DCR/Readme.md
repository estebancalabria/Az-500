# AZ500 — Azure Security Technologies
## Lab 08: Create a Log Analytics Workspace and a Data Collection Rule (DCR)

### Lab scenario

As an Azure Security Engineer for a financial technology company, you are tasked with enhancing monitoring and security visibility across all Azure virtual machines (VMs) used for processing financial transactions and managing sensitive customer data. The security team requires detailed logs and performance metrics from these VMs to detect potential threats and optimize system performance. The CISO has asked you to implement a solution that collects security events, system logs, and performance counters using the Azure Monitor Agent (AMA) along with a Data Collection Rule (DCR).

For all resources in this lab, we use the **East US** region. Confirm with your instructor that this is the region to use for your class/subscription.

> **Note on scope:** This version of the lab is self-contained — it does not assume you completed a previous "Lab 02" or that you're working inside a specific lab-provisioning platform. If your environment already has resources from a different lab sequence, adjust resource group / VM names to avoid collisions.

### Lab objectives

- Exercise 1: Deploy an Azure virtual machine
- Exercise 2: Create a Log Analytics workspace
- Exercise 3: Create a Data Collection Rule
- Exercise 4: Verify the Azure Monitor Agent and confirm data is flowing

---

## Exercise 1: Deploy an Azure virtual machine
**Estimated timing: 10 minutes**

### Task 1: Deploy an Azure virtual machine

1. Sign in to the Azure portal: https://portal.azure.com/

   > Use an account that has the **Owner** or **Contributor** role on the subscription you're using for this lab.

2. Open **Cloud Shell** (icon in the top right of the Azure portal). If prompted, select **PowerShell**.

3. Make sure **PowerShell** is selected in the drop-down at the top-left of the Cloud Shell pane.

4. In the "Getting started" window, leave the default: select a subscription. You can optionally mount a storage account to persist files between sessions — it's not required for this lab.

5. From the **Subscription** drop-down, select the subscription you're using for this lab.

6. Leave **Use an existing private virtual network** unchecked, then click **Apply**.

7. Create a resource group for this lab:

```powershell
New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
```

   > This resource group will be reused if you continue to related labs (Defender for Cloud, JIT VM access, Microsoft Sentinel). If you're running this lab standalone, that's fine too — nothing later in *this* document depends on those.

8. Enable Encryption at Host (EAH):

```powershell
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace Microsoft.Compute
```

9. Create the virtual machine:

```powershell
New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_D2_v4
```

   When prompted for credentials:

   | Setting  | Value |
   |----------|-------|
   | User     | localadmin |
   | Password | Choose a password that meets Azure's complexity requirements (12+ characters, upper/lower case, number, special character). Write it down — you'll need it if you RDP into the VM later. |

   Wait for deployment to complete (a few minutes).

10. Confirm the VM was created successfully:

```powershell
Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
```

    You should see `ProvisioningState: Succeeded`.

11. Close the Cloud Shell pane.

---

## Exercise 2: Create a Log Analytics workspace
**Estimated timing: 10 minutes**

### Task 1: Create a Log Analytics workspace

1. In the Azure portal search bar, type **Log Analytics workspaces** and press Enter.

2. On the Log Analytics workspaces blade, click **+ Create**.

3. On the **Basics** tab, specify:

   | Setting | Value |
   |---|---|
   | Subscription | the subscription you're using for this lab |
   | Resource group | AZ500LAB131415 |
   | Name | lgawIgnite |
   | Region | East US |

   Leave everything else at its default.

4. Click **Review + create**, then **Create**.

---

## Exercise 3: Create a Data Collection Rule
**Estimated timing: 15 minutes**

### Task 1: Create a Data Collection Rule

1. In the Azure portal search bar, type **Monitor** and press Enter.

2. On the Monitor blade, click **Data Collection Rules**.

3. Click **+ Create**.

4. On the **Basics** tab, specify:

   | Setting | Value |
   |---|---|
   | Rule name | DCR1 |
   | Subscription | the subscription you're using for this lab |
   | Resource group | AZ500LAB131415 |
   | Region | East US |
   | Platform type | Windows |
   | Data Collection Endpoint | leave blank |

5. Click **Next: Resources >**.

6. On the Resources page, click **+ Add resources**.

7. In the scope picker, check the box next to your **Subscription** (this will pick up `myVM`). Click **Apply** at the bottom.

8. Click **Next: Collect and deliver >**.

9. Click **+ Add data source**. Set **Data source type** to **Performance Counters**. Leave the default sample rates:

   | Performance counter | Sample rate (seconds) |
   |---|---|
   | CPU | 60 |
   | Memory | 60 |
   | Disk | 60 |
   | Network | 60 |

   Click **Add data source** to confirm this source, then click **+ Add data source** again to add a second one.

10. Set **Data source type** to **Windows Event Logs**. Under **Basic**, check at least:

    | Log group | What it captures |
    |---|---|
    | Security | sign-in attempts, privilege use, audit events |
    | System | OS-level events (services, drivers, hardware) |

    Click **Add data source** to confirm this second source.

    > This is what makes the lab actually deliver on the scenario's promise of "security events, system logs, and performance counters" — without this step, you'd only ever collect performance counters, no security/event data.

11. Click **Next: Destination >**.

12. Click **+ Add destination**. Set **Destination type** to **Azure Monitor Logs**. Confirm your subscription is shown, and set **Account or namespace** to the Log Analytics workspace you created in Exercise 2 (`lgawIgnite`).

13. Click **Add destination**, then make sure the destination is applied to **both** data sources (Performance Counters and Windows Event Logs) — the wizard lets you route each source independently, so double-check both are pointed at `lgawIgnite`.

14. Click **Review + create**, then **Create**.

    Wait a minute or two for the DCR to deploy and associate with the VM.

---

## Exercise 4: Verify the Azure Monitor Agent and confirm data is flowing
**Estimated timing: 10 minutes**

Associating a DCR with a VM scope triggers Azure to automatically install the **Azure Monitor Agent (AMA)** extension on that VM — but it doesn't happen instantly, and it's easy to assume it worked without checking. This exercise is what tells you whether the whole lab actually succeeded.

### Task 1: Confirm the AMA extension installed on the VM

1. In the Azure portal, navigate to **myVM** (Resource group: AZ500LAB131415).

2. In the left menu, under **Settings**, click **Extensions + applications**.

3. Wait a few minutes after creating the DCR, then refresh. You should see an extension named **AzureMonitorWindowsAgent** with status **Provisioning succeeded**.

   > If you don't see it after ~10 minutes, go back to the DCR (DCR1 > Resources) and confirm `myVM` is listed under associated resources. If it's missing, add it manually.

### Task 2: Confirm the DCR association

1. Go back to **Monitor > Data Collection Rules > DCR1**.

2. Click **Resources** in the left menu and confirm `myVM` appears with status **Provisioning state: Succeeded**.

### Task 3: Confirm data is reaching the Log Analytics workspace

1. Navigate to your **lgawIgnite** Log Analytics workspace.

2. In the left menu, click **Logs**. Close the example queries dialog if it appears.

3. Run the following query:

```kusto
Perf
| where ObjectName == "Processor"
| order by TimeGenerated desc
| take 20
```

4. **What you should see:** rows with performance counter data from `myVM` (CPU counters), with a recent `TimeGenerated` timestamp. If the query returns no results, wait 5–10 minutes — there's typically a delay between AMA installing and the first data points landing in the workspace — then re-run the query.

5. Now confirm the Windows Event Logs are arriving too. Run:

```kusto
Event
| where Source == "Microsoft-Windows-Security-Auditing" or EventLog in ("Security", "System")
| order by TimeGenerated desc
| take 20
```

6. **What you should see:** rows from `myVM` with event data — logon events, service starts, etc. — under the `Event` table. If this is empty while `Perf` has data, double-check the Windows Event Logs data source was actually added to DCR1 (Exercise 3, step 10) and that it's routed to `lgawIgnite`.

This confirms the full chain is working: VM → AMA extension → DCR (performance counters + event logs) → Log Analytics workspace.

---

## Results

You deployed an Azure virtual machine, a Log Analytics workspace, and a Data Collection Rule, and confirmed that the Azure Monitor Agent is installed and successfully sending performance counter data to the workspace.

> **Note:** If you plan to continue with related labs (Microsoft Defender for Cloud, just-in-time VM access, Microsoft Sentinel), keep the resources in `AZ500LAB131415` — don't delete them.

---
*Adapted and corrected from Microsoft official AZ-500 courseware for standalone delivery.*
