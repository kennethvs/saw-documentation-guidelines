---
name: Overview
description: "Skill for creating an Overview or Front Page article when a feature or set of related policies requires a parent article before individual policy articles. Used for Properties Catalog overviews, Secure Boot transitions, WDAC strategy, and similar multi-policy feature sets."
---

# Overview / Front Page Skill

Use this skill when a feature or set of related policies needs a parent article explaining the context, design, and related policies before diving into individual policy documentation.

---

## Document Structure

Every Overview article contains these sections in order:

1. **Intro Paragraph**
2. **Documentation Confidence**
3. **Properties Table**
4. **Purpose**
5. **Feature Explanation** (replaces Settings Overview)
6. **How It Works** or **Where to Find the Data**
7. **Security Impact**
8. **Known Impact Table**
9. **Related KB Articles**
10. **Summary**
11. **Microsoft Learn References**

---

## Section Rules

### Intro Paragraph
One paragraph. Must include:
- Feature name (exact casing)
- What the feature is and why it matters
- Scope: which platforms or scenarios it applies to
- Pointer to the related policy articles

**Example:**
> Properties Catalog (Device Inventory) provides read-only inventory data on enrolled devices, including hardware inventory, network adapters, Windows Hello for Business adoption, and BitLocker recovery key escrowing status. This overview explains how Properties Catalog is structured in SAW, how to interpret the data fields, and links to the individual Properties Catalog policy articles that define the inventory scope per platform.

### Documentation Confidence
One of three values with rationale:
- **High** — feature structure verified against portal observation, Microsoft Learn, or production SAW implementation
- **Medium** — structure partially verified; some details inferred from documentation
- **Low** — based on limited source material; specific gaps noted

State what sources were consulted and what remains unverified.

### Properties Table
Include at minimum:
| Property | Value |
|---|---|
| Feature Name | Exact name from portal / Microsoft documentation |
| Feature Type | Inventory / Security / Configuration / etc. |
| Platforms | Windows 10/11, macOS, iOS/iPadOS, Android, etc. |
| Data Scope | What is collected; what is not |
| Refresh Interval | How often data updates |

Add rows as needed for clarification (e.g., Graph API availability, archival options).

### Purpose
Two to three sentences covering:
- What is this feature?
- Why does SAW deploy or depend on it?
- What business or security objective does it serve?

**Example:**
> Properties Catalog provides real-time inventory visibility into device hardware, configuration, and compliance state without requiring a separate asset management system. SAW uses Properties Catalog to detect hardware changes, monitor BitLocker key escrowing, and validate Windows Hello for Business adoption across the population.

### Feature Explanation
Replace "Settings Overview" with a narrative explanation of the feature.

Cover:
- **What it collects** — inventory categories, data refresh frequency
- **How it works** — the data flow (collection → storage → query)
- **Data availability** — where admins can access it (Intune portal, queries, exports)
- **Limitations** — what is not collected, platform constraints

Do not list individual settings here (that belongs in the policy articles). Focus on the feature as a whole.

**Example:**
> Properties Catalog collects device inventory on a regular schedule (typically hourly or daily depending on the property). Collected data is available in the Intune admin center under Devices > All Devices > [Device] > Hardware or through advanced queries in the portal. Data is also available for export via Microsoft Graph API and queryable in Log Analytics if Intune diagnostic logs are forwarded to a workspace. Properties vary by platform: Windows devices report hardware inventory, network adapters, and BitLocker state; macOS devices report hardware and MDM enrollment status; iOS/iPadOS devices report limited hardware info due to platform constraints; Android reports device model and enrollment method.

### How It Works / Where to Find the Data
Create a new `<h2>` section with step-by-step guidance or a data navigation map.

**For inventory features:** Show where admins navigate to view the data.

**For multi-policy features:** Explain the overall workflow or sequence.

**Example:**
```
## Where to Find Properties Catalog Data

1. Intune Admin Center → Devices → All Devices
2. Select a device → Hardware tab
3. Scroll to view properties grouped by category:
   - Device information (model, serial, manufacturer)
   - Processor and memory
   - Network adapters
   - Storage
   - BitLocker status

Alternatively, export all properties for all devices:
1. Devices → All Devices → Export
2. Filter to include Properties Catalog columns
3. Download CSV for analysis in Excel or Power BI
```

### Security Impact
One paragraph covering:
- What security or operational visibility does this feature enable?
- Are there compliance implications?
- What risks does it mitigate or expose?
- Is the data sensitive (e.g., BitLocker keys, network topology)?

**Example:**
> Properties Catalog provides the visibility required to enforce hardware security standards (TPM version, Secure Boot state, BitLocker encryption) and to audit compliance with licensing and support requirements (OS version, processor, RAM). BitLocker recovery key escrow data is sensitive and must be treated as secret material. Unauthorized access to device inventory could expose information useful for targeted attacks (e.g., identifying devices with older processors or missing security features). Restrict Properties Catalog data access to authorized administrators only.

### Known Impact Table
Two columns: **Component** and **Impact**.

List:
- Systems that depend on or integrate with this feature
- Explicit non-impacts (to clarify what this feature does NOT do)
- Data sensitivity and access control considerations
- Platform-specific limitations

**Example:**
| Component | Impact |
|---|---|
| Intune admin center | Properties Catalog data is read-only. Admins can view but not modify hardware inventory. |
| Microsoft Graph API | All Properties Catalog data is available via Graph; Microsoft Entra ID role-based access control applies. |
| Defender for Endpoint | Device inventory from MDE is separate from Properties Catalog. Both sources may be used for compliance queries. |
| BitLocker recovery | BitLocker recovery keys escrowed via Intune are stored in Azure AD and queryable via Graph. Keys are sensitive and require RBAC. |
| Windows Hello for Business | WH4B adoption status is visible in Properties Catalog; used by SAW to monitor rollout progress. |
| Mobile device inventory | iOS/iPadOS and Android device inventory is limited due to platform restrictions; full hardware details not available. |

### Related KB Articles
Create a new `<h2>` section listing all child articles.

Format as a table or bulleted list with links:

**Example:**
```
## Related KB Articles

- [Policy Article 1: Properties Catalog — Windows Devices](#)
- [Policy Article 2: Properties Catalog — macOS Devices](#)
- [Policy Article 3: Properties Catalog — iOS/iPadOS Devices](#)
- [How-To: Export and Analyze Properties Catalog Data](#)
```

Or as a table:

| Article | Purpose |
|---|---|
| [W1x - CP - Properties Catalog - Windows - v1.0](#) | Defines device inventory collection scope for Windows 10/11 |
| [M1x - CP - Properties Catalog - macOS - v1.0](#) | Defines device inventory collection scope for macOS |
| [iOS - CP - Properties Catalog - iOS - v1.0](#) | Defines device inventory collection scope for iOS/iPadOS |

### Summary
Two paragraphs:

**Paragraph 1:** Restate what the feature is, its purpose in SAW, and the key organizational decisions around its deployment.

**Paragraph 2:** Operational notes including:
- How Properties Catalog data is used in day-to-day operations
- Any manual review or approval processes tied to this data
- Links to related documentation (e.g., the Properties Catalog policy articles)
- Versioning or update strategy

**Example Paragraph 1:**
> Properties Catalog is the read-only inventory backbone of SAW's hardware compliance and asset management strategy. It provides real-time visibility into device hardware, configuration, and security features (BitLocker, Secure Boot, TPM) without requiring a separate asset management tool or integration. Properties Catalog data is the source of truth for hardware compliance reporting and licensing validation.

**Example Paragraph 2:**
> Properties Catalog policies are deployed per platform (Windows, macOS, iOS/iPadOS, Android) and define which properties are collected and at what refresh interval. All related policy articles (see Related KB Articles section) must be reviewed together to understand the full Properties Catalog footprint. Data is available in the Intune admin center, via Microsoft Graph, and via export for analysis. Admins should review hardware compliance queries monthly and investigate devices with missing security features (TPM, Secure Boot, BitLocker). This overview article is the authoritative reference for Properties Catalog scope and strategy in SAW.

### Microsoft Learn References
One `<li>` per link. Use the full page title as link text.

For Joost Gelijsteen references, include as additional resource after Learn links.

**Example:**
```html
<ul>
  <li><a href="https://learn.microsoft.com/en-us/mem/intune/devices/device-inventory">Device inventory in Microsoft Intune</a></li>
  <li><a href="https://learn.microsoft.com/en-us/graph/api/resources/devicemanagementscriptstatesummary">Device inventory data via Microsoft Graph</a></li>
  <li><a href="https://joostgelijsteen.com/properties-catalog-strategy">Properties Catalog Strategy Guide — Joost Gelijsteen</a></li>
</ul>
```

---

## Content Rules

**Accuracy first.** Verify feature structure against Microsoft Learn or production observation. If details cannot be confirmed, state so in the confidence note.

If key author fields are null or missing (for example feature purpose text, SAW rationale, or version change notes), ask the user for those values first. Do not infer these fields from Microsoft Learn unless the user explicitly approves inference.

**No fabrication.** Do not describe features or workflows that do not exist.

**Portal terminology.** Use exact names:
- "Properties Catalog" not "Device Inventory"
- "Intune admin center" not "MEM portal"
- Exact feature names as they appear in the portal

**Link verification.** Every link must point to a verified, existing URL.

**Completeness.** Related KB Articles section must list all child articles. If child articles do not yet exist, note "To be documented" or "Planned."

---

## Questions to Ask Before Starting

1. Based on this JSON, what do you want me to generate?
2. Is this overview for a multi-policy feature set, platform-specific feature, or a specific compliance/security capability?
3. Are there existing policy articles related to this feature that should be listed in Related KB Articles?
4. Who is the primary audience — internal SAW team, customers, or both?

---

## Output Formats

This skill supports conversion to:
- **HTML** — for ProProfs KB parent article
- **DOCX** — for Word document or offline reference

Both formats use the same section structure above.
