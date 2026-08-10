---
name: How-To
description: "Skill for step-by-step operational procedures in SAW environments. Used for onboarding tasks, backup/restore procedures, policy deployment workflows, customer handoff, and other repeatable processes."
---

# How-To / Procedural Skill

Use this skill when documenting a step-by-step operational procedure, workflow, or process that SAW administrators or MSP technicians will follow repeatedly.

---

## Document Structure

Every How-To article contains these sections in order:

1. **Intro Paragraph**
2. **Documentation Confidence**
3. **Properties Table**
4. **Purpose**
5. **Prerequisites**
6. **Steps**
7. **Verification**
8. **Troubleshooting** (if applicable)
9. **Summary**
10. **Related Articles / References**

---

## Section Rules

### Intro Paragraph
One paragraph. Must include:
- What procedure this document describes
- When an administrator would follow this procedure
- Scope: which scenarios or platforms this applies to
- Expected outcome

**Example:**
> This procedure describes how to manually trigger and download the Secure at Work device configuration backup for a customer tenant. Backup exports device groups, policy assignments, and configuration profiles in a format compatible with the intune-backup pipeline for drift detection and historical comparison. Use this procedure when automated scheduled backups are unavailable or when an on-demand backup is required for compliance or troubleshooting.

### Documentation Confidence
One of three values:
- **High** — procedure tested in production or staging environment and verified to work as documented
- **Medium** — procedure based on documented process; tested in limited scenarios
- **Low** — procedure based on documentation or guidance; not fully tested in all environments

State what testing has been done and any assumptions.

### Properties Table
Always include:
| Property | Value |
|---|---|
| Procedure Name | Exact name as used in operational runbooks |
| Complexity | Simple / Moderate / Advanced |
| Time Required | Estimated duration (e.g., 10-15 minutes) |
| Prerequisites | List key requirements |
| Platforms | Windows, macOS, Intune, Graph API, Azure, etc. |
| Risk Level | Low / Moderate / High |
| Reversible | Yes / No / Partial |

Add rows as appropriate:
- Required Permissions (e.g., Intune Service Administrator)
- Supported Versions (e.g., Windows 10 21H2+)
- Tools Required (e.g., PowerShell, intune-backup CLI)

### Purpose
Two to three sentences covering:
- What does this procedure accomplish?
- Why is this procedure necessary?
- What are the business or operational goals?

**Example:**
> The manual backup procedure ensures that SAW device configuration can be exported on-demand for compliance audits, change tracking, or disaster recovery preparation. Automated backups may fail or have gaps; a manual export verifies the current tenant state and creates a timestamped snapshot for comparison against previous versions. Manual backups are also useful for customer handoff and historical record-keeping.

### Prerequisites
Replace "Security Impact" with a bulleted or numbered list of prerequisites.

Cover:
- **Required permissions** — Intune Service Administrator, Graph API permissions, etc.
- **Required tools** — PowerShell, intune-backup CLI, Azure CLI, etc.
- **Access requirements** — Network access, MFA enrollment, VPN, etc.
- **Environmental assumptions** — Tenant configuration, group setup, policy existence, etc.
- **Preparatory steps** — any actions that must be completed before starting the main procedure

**Example:**
```
## Prerequisites

- **Permissions**: Intune Service Administrator or equivalent role with read access to all device groups and policies
- **Tools**: 
  - intune-backup CLI (version 2.0+) installed on local machine
  - PowerShell 5.1+ or PowerShell 7.x
  - Microsoft Graph PowerShell SDK (Install-Module Microsoft.Graph)
- **Access**:
  - Network access to https://graph.microsoft.com and https://intune.microsoft.com
  - Azure AD credentials with MFA enrolled (if required by tenant Conditional Access)
- **Tenant setup**:
  - SAW groups (AAD_UG_ModernWorkplace, AAD_DG_* groups) must exist
  - At least one policy assigned to test the export
- **Preparation**:
  - Open PowerShell as Administrator
  - Authenticate to Microsoft Graph: `Connect-MgGraph -Scopes "DeviceManagementConfiguration.Read.All"`
  - Verify connectivity: `Get-MgOrganization`
```

### Steps
Numbered list. One step per action.

Format: **Step title** — detailed instruction. Use imperative mood ("Click", "Run", "Navigate"). Include screenshots or code blocks as needed.

For longer procedures, use sub-steps (a, b, c).

**Example:**
```
## Steps

1. **Authenticate to Microsoft Graph**
   PowerShell will prompt for credentials. Use an account with Intune Service Administrator privileges.
   ```
   Connect-MgGraph -Scopes "DeviceManagementConfiguration.Read.All", "DeviceManagementManagedDevices.Read.All"
   ```

2. **Download and initialize the intune-backup CLI**
   Clone or update the intune-backup repository:
   ```
   git clone https://github.com/SAW-JGelijsteen/intune-backup.git
   cd intune-backup
   npm install
   ```

3. **Verify your tenant is configured**
   List all device groups to confirm SAW groups exist:
   ```
   Get-MgGroup -Filter "displayName startswith 'AAD_DG_'" | Select-Object DisplayName, Id
   ```
   Expect to see at least:
   - AAD_DG_AutoPilot-Devices
   - AAD_DG_AutoPilot-Devices-Shared
   - AAD_DG_AutoPilot-Devices-KioskMulti

4. **Run the backup export**
   ```
   npm run backup -- --output ./backup-$(Get-Date -Format 'yyyyMMdd-HHmmss')
   ```
   Expected output: "Backup complete. Exported X policies, Y groups, Z assignments."

5. **Verify the backup directory structure**
   Navigate to the generated backup directory and confirm these subdirectories exist:
   - `deviceGroups/`
   - `policies/`
   - `assignments/`
   - `metadata.json`

6. **Upload the backup to your repository or storage location** (if required)
   ```
   git add backup-*
   git commit -m "Manual backup: $(Get-Date -Format 'yyyyMMdd-HHmmss')"
   git push
   ```
```

### Verification
Create a new `<h2>` section describing how to verify the procedure completed successfully.

Cover:
- **Expected outputs** — what should you see when the procedure succeeds?
- **Verification checks** — commands or portal actions to confirm the result
- **Success criteria** — specific indicators that the procedure worked

**Example:**
```
## Verification

**Expected output:**
- Backup directory created with timestamp in the name
- `metadata.json` contains current date and list of exported policies
- No errors in PowerShell console (exit code 0)

**Verification checks:**
1. Confirm the backup directory exists and is not empty:
   ```
   ls -la backup-*/
   ```
   Expected: multiple subdirectories (deviceGroups, policies, assignments) and metadata.json

2. Confirm all device groups were exported:
   ```
   wc -l backup-*/deviceGroups/*.json
   ```
   Expected: at least 10 groups exported (specific count depends on your tenant)

3. Spot-check a policy export:
   ```
   cat backup-*/policies/W1x-*.json | head -20
   ```
   Expected: valid JSON with policy name, settings, and version visible

**Success criteria:**
- Backup directory is created and contains all expected subdirectories
- metadata.json lists all policies, groups, and assignments
- No errors or warnings in PowerShell output
- File timestamps show current date/time
```

### Troubleshooting (if applicable)
Create a new `<h2>` section covering common issues.

Format:

| Problem | Cause | Solution |
|---|---|---|
| Authentication fails with "Unauthorized" | Insufficient permissions or MFA challenge | Re-authenticate with `Connect-MgGraph -ForceRefresh` or check Conditional Access policies |
| Backup directory is empty | Policies not assigned or groups not found | Verify groups exist: `Get-MgGroup -Filter "displayName startswith 'AAD_DG_'"` |
| Export completes but metadata is missing | Backup script interrupted or incomplete | Re-run the backup command; check for network interruptions |

**Example:**
```
## Troubleshooting

| Problem | Cause | Solution |
|---|---|---|
| "Connect-MgGraph: The term 'Connect-MgGraph' is not recognized" | Microsoft Graph PowerShell SDK not installed | `Install-Module Microsoft.Graph -Force; Import-Module Microsoft.Graph` |
| "Permission Denied" when accessing policies | Insufficient Intune permissions | Confirm your account is assigned Intune Service Administrator or Device Administrator role |
| Backup export is empty (0 policies) | Policies are not assigned to any groups | Check assignment scope in Intune admin center; verify SAW groups have assigned policies |
| Backup script times out | Large tenant or slow network | Increase timeout: `npm run backup -- --timeout 120000` (120 seconds) |
| "AAD_DG_* groups not found" | Device groups have not been created | Create groups in Azure AD following SAW naming conventions, or run the SAW onboarding procedure |
```

### Summary
Two paragraphs:

**Paragraph 1:** Restate what the procedure accomplishes and confirm the key steps.

**Paragraph 2:** Operational notes including:
- Frequency of execution (weekly, monthly, on-demand, etc.)
- Dependencies on other procedures
- Escalation path if the procedure fails
- Related procedures to follow up with

**Example Paragraph 1:**
> The manual backup procedure exports all SAW device groups, policies, and assignments into a timestamped directory structure compatible with intune-backup analysis. The procedure is straightforward when prerequisites are met and provides a full snapshot of the current tenant configuration. Backups are valuable for compliance audits, drift detection, and historical record-keeping.

**Example Paragraph 2:**
> This procedure can be run on-demand whenever a configuration change is completed, before major tenant updates, or on a scheduled basis (weekly or monthly). The exported backup files can be committed to a Git repository for version control and change tracking. If the backup fails, check network connectivity, permissions, and authentication status before retrying. For automated backups, refer to the intune-backup scheduled pipeline setup procedure. After a successful backup, run the drift-detection procedure to compare against the previous backup and identify any configuration changes.

### Related Articles / References
One `<li>` per link. Include:
- Links to related procedures
- Microsoft Learn documentation for the tools or features used
- SAW-specific documentation

**Example:**
```html
<ul>
  <li><a href="#">How-To: Set Up Automated Device Configuration Backups</a></li>
  <li><a href="#">How-To: Run Drift Detection on Backup Exports</a></li>
  <li><a href="https://learn.microsoft.com/en-us/powershell/module/microsoft.graph">Microsoft Graph PowerShell Module Reference</a></li>
  <li><a href="https://github.com/SAW-JGelijsteen/intune-backup">SAW intune-backup GitHub Repository</a></li>
</ul>
```

---

## Content Rules

**Accuracy first.** Test the procedure in a staging or non-production environment before documenting. Verify every command and every step works as documented.

If key author fields are null or missing (for example procedure purpose, expected outcome, or rollback guidance), ask the user for those values first. Do not infer these fields from Microsoft Learn unless the user explicitly approves inference.

**Clarity over brevity.** Use clear, step-by-step language. Include full code blocks (not abbreviated). Assume the reader is not familiar with the procedure.

**Screenshots and examples.** Include portal screenshots, code snippets, and expected output to help readers confirm they are on the right track.

**Error handling.** Anticipate common errors and include troubleshooting steps.

**Reversibility.** State clearly whether the procedure can be reversed and what steps are required to undo it (if applicable).

**No invented tools or URLs.** Every tool, CLI, or repository must exist and be verified.

---

## Questions to Ask Before Starting

1. Based on this JSON, what do you want me to generate?
2. What is the intended audience — internal SAW team, MSP technicians, or customers?
3. Has this procedure been tested end-to-end? In which environments?
4. What are the most common issues or failure points?
5. How frequently will this procedure be executed?
6. Is the procedure reversible, and should rollback instructions be included?

---

## Output Formats

This skill supports conversion to:
- **HTML** — for ProProfs KB operational documentation
- **DOCX** — for runbooks, training materials, or offline reference

Both formats use the same section structure above.
