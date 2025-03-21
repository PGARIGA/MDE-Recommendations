## Implementing User Account Control (UAC) Policy: Automatically Deny Elevation Requests

### Overview
To enhance security and reduce the risk of privilege escalation attacks, we are implementing a policy to **automatically deny elevation requests for standard users**. This configuration aligns with **Microsoft Defender for Endpoint (MDE) security recommendations**.

### Implementation Methods

#### 1. Group Policy (GPO) - Recommended for Domain-Joined Devices
1. Open **Group Policy Management Editor** (`gpedit.msc`).
2. Navigate to:
   ```
   Computer Configuration → Windows Settings → Security Settings → Local Policies → Security Options
   ```
3. Locate **"User Account Control: Behavior of the elevation prompt for standard users"**.
4. Set it to **"Automatically deny elevation requests"**.
5. Click **Apply** and **OK**.
6. Run the following command to apply the policy immediately:
   ```sh
   gpupdate /force
   ```

#### 2. Registry Modification - For Standalone Machines
1. Open **Registry Editor** (`regedit.exe`).
2. Navigate to:
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
   ```
3. Find the key **ConsentPromptBehaviorUser**.
4. Set its value to:
   ```sh
   0  (Automatically deny elevation requests)
   ```
5. Restart the system for the changes to take effect.

Alternatively, execute the following PowerShell command:
```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name "ConsentPromptBehaviorUser" -Value 0
```

#### 3. Microsoft Intune / Endpoint Manager - For Cloud-Managed Devices
1. **Create a Device Configuration Profile**
   - **Platform**: Windows 10 and later
   - **Profile Type**: Settings Catalog
   - Search for **User Account Control** settings.
   - Set **User Account Control: Behavior of the elevation prompt for standard users** to **Automatically deny elevation requests**.
   - Assign to target devices.

2. **Save and Deploy**.

### Rollback / Revert Changes
#### GPO Rollback
1. Reopen Group Policy Management.
2. Set **"User Account Control: Behavior of the elevation prompt for standard users"** back to **"Prompt for credentials on the secure desktop"**.
3. Run:
   ```sh
   gpupdate /force
   ```

#### Registry Rollback
Execute:
```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name "ConsentPromptBehaviorUser" -Value 1
```
or manually set **ConsentPromptBehaviorUser = 1** in the registry.

#### Intune Rollback
1. Remove the assigned policy from Endpoint Manager.
2. Devices will revert to their previous settings.

### Impact & Considerations
- Standard users will **not be able to elevate privileges**.
- IT support teams may need **administrative accounts** to perform tasks requiring elevation.
- Applications requiring **UAC elevation** might need configuration changes.

### Next Steps
- **Assess the impact** on end-user workflows.
- **Confirm compatibility** with IT operations.
- **Monitor logs** for issues post-deployment.

---
**Author**: [Your Name]  
**Date**: [YYYY-MM-DD]  
**Company**: [Your Company Name]
