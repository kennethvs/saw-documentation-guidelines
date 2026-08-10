---
name: Policy-Article
description: "Skill for converting a single Intune configuration policy, compliance policy, remediation script, or settings catalog policy into SAW KB article format (HTML or DOCX). Standard article for a single policy with complete technical specification and security impact assessment."
---

# Policy Article Skill

Use this skill when documenting a single Intune policy, configuration profile, compliance policy, remediation script, or settings catalog policy.

---

## Document Structure

Every Policy Article contains these sections in order:

1. **Intro Paragraph**
2. **Documentation Confidence**
3. **Properties Table**
4. **Purpose**
5. **Settings Overview**
6. **Security Impact**
7. **Known Impact Table**
8. **Summary**
9. **Microsoft Learn References**

---

## Section Rules

### Intro Paragraph
One paragraph. Must include:
- Policy full name (exact casing from Intune portal)
- What it does and which platform it targets
- Where it fits in the SecureAtWork baseline
- If part of a multi-article set, reference the overview article

**Example:**
> The W1x - CP - WDAC - Audit - v1.0 configuration profile enforces Windows Defender Application Control (WDAC) in audit mode on Windows 10 and 11 devices. This policy is part of the SAW security baseline, serving as the foundation for application allowlisting before transitioning to enforcement mode. For background on WDAC strategy, see the [WDAC Overview](#) article.

### Documentation Confidence
One of three values, with rationale:
- **High** — settings directly observed from portal screenshots, JSON exports, or confirmed against Microsoft Learn
- **Medium** — settings inferred from partial source; Microsoft Learn consulted but some details extrapolated
- **Low** — settings reconstructed from description only; state what is unverified

Never claim High confidence on settings not verified. State assumptions clearly.

### Properties Table
Always include at minimum:
| Property | Value |
|---|---|
| Policy Name | Exact name from portal |
| Policy Type | Configuration Profile / Compliance Policy / Remediation / etc. |
| Platform | Windows 10/11, macOS, iOS, Android, Linux, etc. |
| Profile Type | Device / User / Shared Device |
| Version | v1.0, v1.1, etc. |
| Scope Tags | Default (or other if applicable) |

Add rows as appropriate:
- Risk Classification (if a known gap article)
- Assignment Model (UG/DG-based)
- Remediation Available (Yes/No for compliance)

### Purpose
Two to three sentences answering:
- What does this policy do?
- Why does SAW deploy it?
- What operational or security goal does it serve?

**Example:**
> WDAC audit mode collects telemetry on application launch attempts without blocking execution. This allows organizations to understand application inventory and usage patterns before enforcing a whitelist. The audit phase is mandatory before enforcement and typically runs for 2-4 weeks.

### Settings Overview
Numbered list. One `<li>` per setting or setting group.

Format: **Setting Name** — one to two sentences explaining what it controls and why it is configured the way it is. For Properties Catalog policies, state the refresh interval per item.

Do not list settings you cannot verify. Flag unconfirmed settings or omit.

**Example:**
> 1. **Audit Policy** — Set to Audit to collect application launch telemetry without blocking. Must remain in audit mode for minimum 2 weeks before enforcement.
> 2. **Refresh Interval** — 30 minutes. Updates the policy and WDAC rules periodically without requiring device restart.

### Security Impact
One paragraph covering:
- Is this policy enforcing, restricting, or collecting?
- What is the user-facing impact? (performance, UX, blocked actions)
- What security objective does it serve?
- If read-only, state so explicitly.

**Example:**
> WDAC audit mode is non-blocking; no applications are prevented from running. Audit logs are collected in Event Viewer and forwarded to Defender for Endpoint for analysis. The security objective is visibility into application inventory before enforcement. User experience is not affected.

### Known Impact Table
Two columns: **Component** and **Impact**.

List every component or surface that is:
- Meaningfully affected by the policy
- Explicitly not affected (when important to clarify)
- Incompatible or requires special handling

For known gap articles, list each evaluated mitigation and its outcome.

Use `&amp;` instead of `&` in HTML.

**Example:**
| Component | Impact |
|---|---|
| Defender for Endpoint | WDAC audit logs are forwarded to M365 Defender; visible in Device Timeline and Advanced Hunting |
| BitLocker | No interaction. BitLocker and WDAC operate independently. |
| Third-party antivirus | Third-party products cannot integrate with WDAC. Disable third-party protection if WDAC enforcement is active. |
| User experience | No impact in audit mode. Users are not notified of policy deployment. |

### Summary
Two paragraphs:

**Paragraph 1:** Restate what the policy does, confirm key configuration decisions, and validate the reasoning.

**Paragraph 2:** Operational notes including:
- Versioning expectations (major vs. minor)
- Manual steps required (if any)
- Pipeline exclusions (if Graph-incompatible)
- Review triggers (conditions that warrant policy update)
- Dependencies or sequencing notes

**Example Paragraph 1:**
> WDAC audit mode is the mandatory entry point for application allowlisting in SAW. This policy collects application launch telemetry over 2-4 weeks before enforcement rules are applied. Audit logs are forwarded to Defender for Endpoint and queryable in Advanced Hunting, enabling data-driven decision-making before enforcement.

**Example Paragraph 2:**
> Version v1.0 is the baseline audit policy. A transition to v2.0 (enforcement mode) is triggered only after audit data confirms acceptable application inventory and no critical business applications are at risk. Policy updates are documented in the version history and superseded versions are retained for rollback reference. This policy is included in the intune-backup automation pipeline and may be created via Microsoft Graph.

### Microsoft Learn References
One `<li>` per link. Use the full page title as link text. Always link to the relevant Learn page, not the homepage.

For content referencing Joost Gelijsteen (joostgelijsteen.com), include as additional reference after Learn links.

**Example:**
```html
<ul>
  <li><a href="https://learn.microsoft.com/en-us/windows/security/application-security/application-control/windows-defender-application-control/wdac-and-applocker-overview">Windows Defender Application Control overview</a></li>
  <li><a href="https://learn.microsoft.com/en-us/windows/security/application-security/application-control/windows-defender-application-control/design/audit-windows-defender-application-control-policies">Audit Windows Defender Application Control policies</a></li>
  <li><a href="https://joostgelijsteen.com/wdac-audit-strategy">WDAC Audit Strategy — Joost Gelijsteen</a></li>
</ul>
```

---

## Content Rules

**Accuracy first.** Never invent setting values, registry keys, OMA-URI paths, or CSP references. If source material is unavailable:
- Search Microsoft Learn to verify
- State clearly in confidence note what was and was not verified
- Omit unverified details rather than guess

If key author fields are null or missing (for example policy description, purpose rationale, or version change notes), ask the user for those values first. Do not infer these fields from Microsoft Learn unless the user explicitly approves inference.

**No fabrication.** If asked about a setting that does not exist in Intune, say so. Do not describe a setting that cannot be verified.

**Portal terminology.** Use exact terminology from Intune admin center:
- "Settings Catalog" not "settings catalog policy"
- "Properties Catalog" not "Device Inventory profile"
- "Microsoft Entra ID" not "Azure AD"
- "Intune admin center" not "MEM portal"

**Graph API wall.** Properties Catalog profiles and any policy not reachable via Microsoft Graph must include a note in Known Impact:
> Policy cannot be created, read, or modified via Microsoft Graph API. Manual portal creation is required.

Also note that these policies are excluded from intune-backup and intune-policy-docs automation pipelines.

**Pipeline exclusions.** If a policy is excluded from automated backup (intune-backup repo) or documentation pipeline (intune-policy-docs repo), state this in Known Impact and confirm in Summary that this article is the authoritative documentation.

**No em dashes.** Use regular hyphens or restructure. Em dashes (—) are acceptable only in table cells.

**No invented links.** Every hyperlink must point to a URL verified to exist. For Microsoft Learn, use the canonical `https://learn.microsoft.com/en-us/mem/intune/...` path.

---

## Questions to Ask Before Starting

If the user provides a policy name but no source material:

1. Can you share a screenshot of the configuration settings from the Intune portal, or a JSON export of the policy?
2. Based on this JSON, what do you want me to generate?
3. What is the intended audience — internal IT team, customer-facing KB, or both?
4. Is this policy part of a multi-article set (e.g., related policies in a feature category)?

If source material is provided, proceed directly—do not ask for what is already visible.

---

## Output Formats

This skill supports conversion to:
- **HTML** — for direct ProProfs KB import
- **DOCX** — for Word document import or offline review

Both formats use the same section structure above.
