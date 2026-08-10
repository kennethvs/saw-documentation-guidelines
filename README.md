# SAW Documentation Guidelines

Standard frameworks, skills, and workflows for creating Secure at Work (SAW) Knowledge Base articles. This repository enables consistent, high-quality documentation across all SAW platforms and features.

---

## Overview

This repository contains:

- **Skill files** — Structured templates for five document types (Policy Article, Overview, Known Gap, How-To, Description Field)
- **Conversion workflows** — Integration with Claude app for automated markdown-to-HTML/DOCX conversion and direct Intune Description field generation
- **Standards and rules** — Content accuracy, portal terminology, and structural consistency

Documentation is **source-controlled in Markdown**, then **converted on-demand** to HTML (for ProProfs KB) or DOCX (for Word import/offline review). Description Field requests are returned directly as a pipe-delimited Intune Description field value.

---

## Quick Start

### For Kenneth & Joost: Using the Conversion Project

1. **Create or update a `.md` file** in your SAW documentation directory (following the guidelines in the relevant skill file)
2. **Open the SAW Documentation Conversion Project** in Claude app
3. **Upload the `.md` file** and specify the document type:
   - `Policy Article` — Single Intune policy
   - `Overview` — Feature overview with related policies
   - `Known Gap` — Platform limitation assessment
   - `How-To` — Operational procedure
4. **Answer clarifying questions** (asked one at a time)

Important: if you provide JSON input, the assistant should first ask: "Based on this JSON, what do you want me to generate?" before drafting any output.
5. **Choose output format:** HTML (ProProfs KB) or DOCX (Word). For Description Field, no format choice is needed.
6. **Download and import** the converted document

The Claude Project automatically fetches the latest skill file from this repository and applies it to your document.

---

## Document Types & Skills

### 1. Policy Article
**Use for:** Single Intune configuration profile, compliance policy, remediation script, or settings catalog policy.

**Structure:** Intro → Confidence → Properties → Purpose → Settings Overview → Security Impact → Known Impact → Summary → References

**File:** `Skills/SKILL-Policy-Article.md`

**Example use cases:**
- W1x - CP - WDAC - Audit - v1.0
- M1x - CP - Delay Software Updates - v1.0
- iOS - CP - VPN Configuration - v1.0

---

### 2. Overview
**Use for:** Feature overview or parent article when multiple related policies need context.

**Structure:** Intro → Confidence → Properties → Purpose → Feature Explanation → How It Works → Security Impact → Known Impact → Related KB Articles → Summary → References

**File:** `Skills/SKILL-Overview.md`

**Example use cases:**
- Properties Catalog Overview (with links to platform-specific policies)
- WDAC Strategy Overview (with links to audit + enforcement policies)
- Secure Boot Certificate Migration Overview

---

### 3. Known Gap
**Use for:** Platform limitation or missing capability that has been assessed and formally accepted.

**Structure:** Intro → Confidence → Properties → Purpose → Gap Description → Risk Assessment → Known Impact → Decision → Summary → References

**File:** `Skills/SKILL-Known-Gap.md`

**Example use cases:**
- "Company Portal cannot rename Android Enterprise devices post-enrollment"
- "iOS/iPadOS does not support full disk encryption via MDM"
- "Conditional Access does not recognize guest identities in certain scenarios"

---

### 4. How-To
**Use for:** Step-by-step operational procedure, workflow, or runbook.

**Structure:** Intro → Confidence → Properties → Purpose → Prerequisites → Steps → Verification → Troubleshooting → Summary → Related Articles

**File:** `Skills/SKILL-How-To.md`

**Example use cases:**
- "How to manually backup Intune device configuration"
- "How to onboard a new customer tenant to SAW"
- "How to deploy a policy update through ring stages"

---

### 5. Description Field
**Use for:** Generating a single Intune Description field value for a SAW policy in the exact pipe-delimited portal style.

**Structure:** Final pipe-delimited Description field string with optional Category and Version-change segments, returned in a single copy-paste-ready `text` block.

**File:** `Skills/SKILL-Description-Field.md`

**Example use cases:**
- Normalize a portal description into the exact Intune field format
- Generate a new policy description with `|` separators preserved
- Remove HTML styling from an exported description field while keeping the visible structure

---

## Repository Structure

```
kennethvs/saw-documentation-guidelines/
├── Skills/
│   ├── SKILL-Policy-Article.md
│   ├── SKILL-Overview.md
│   ├── SKILL-Known-Gap.md
│   ├── SKILL-How-To.md
│   └── SKILL-Description-Field.md
├── README.md (this file)
├── PROJECT-SYSTEM-PROMPT.md (Claude Project configuration)
└── examples/ (optional: example articles in each category)
```

---

## Content Rules (All Document Types)

These rules apply across all skills and documents:

### Accuracy
- Never invent setting values, registry keys, OMA-URI paths, or CSP references
- If source material is unavailable, search Microsoft Learn before documenting
- State assumptions clearly in the Documentation Confidence section
- Omit unverified details rather than guess
- If key author fields are missing or null (for example description, purpose, or version change notes), ask for user-provided values first; only infer from Microsoft Learn when explicitly approved by the user

### No Fabrication
- Do not describe settings or features that do not exist
- Do not speculate about future Microsoft features or timelines
- If uncertain, flag the content as "Low" confidence and explain what is unverified

### Portal Terminology
Use exact names from Intune admin center:
- "Settings Catalog" (not "settings catalog policy")
- "Properties Catalog" (not "Device Inventory profile")
- "Microsoft Entra ID" (not "Azure AD")
- "Intune admin center" (not "MEM portal" or "Endpoint Manager")
- Exact casing for group names: `AAD_UG_ModernWorkplace`, not `aad_ug_modernworkplace`

### Graph API Transparency
If a policy type cannot be created/read/modified via Microsoft Graph (e.g., Properties Catalog):
- Include a note in the Known Impact table: *Policy cannot be created, read, or modified via Microsoft Graph API. Manual portal creation is required.*
- State that the policy is excluded from intune-backup and intune-policy-docs automation pipelines

### No Em Dashes
- Use regular hyphens (-) in body text
- Em dashes (—) are acceptable only in table cells
- Restructure sentences rather than relying on em dashes for clarity

### Link Verification
- Every hyperlink must point to a verified, existing URL
- For Microsoft Learn, always use canonical paths: `https://learn.microsoft.com/en-us/mem/intune/...`
- Do not construct URLs by guessing; verify before linking

---

## Naming Conventions

### Policy Names
Follow this pattern: `<Platform> - <Component> - <Purpose/Scope> - v<Major.Minor>`

Examples:
- `W1x - CP - WDAC - Audit - v1.0` (Windows Configuration Profile, WDAC, Audit mode)
- `M1x - CP - Delay Software Updates - Ring 1 - v1.0` (macOS, Configuration Profile, Ring 1 staging)
- `iOS - CP - VPN Configuration - v1.0` (iOS Configuration Profile, VPN)

### Platform Prefixes
| Prefix | Meaning |
|--------|---------|
| `W1x` | Windows 10 and 11 |
| `M1x` | macOS |
| `iOS` | iOS/iPadOS |
| `And` | Android Enterprise |

### Component Types
| Abbreviation | Meaning |
|---|---|
| `CP` | Configuration Profile |
| `CMP` | Compliance Policy |
| `REM` | Remediation Script (detection + remediation pair) |
| `Script` | Platform Script (proactive, no remediation counterpart) |
| `APP` | App deployment |

### Versioning
- Major version (v1.0, v2.0): Replaces the old policy entirely
- Minor version (v1.1, v1.2): Extends or clarifies the policy without replacement
- Document version expectations in the Summary section

---

## Working With the Claude Project

### Setting Up the Project (One Time)

1. **Create a new Claude Project** in Claude.ai
2. **Set the system prompt** to the content in `PROJECT-SYSTEM-PROMPT.md`
3. **Name the project:** "SAW Documentation Conversion"
4. **Project instructions:** Add context that this project converts SAW documentation using the latest skills from `https://github.com/kennethvs/saw-documentation-guidelines`

### Using the Project (Every Time)

1. **Upload or paste** your `.md` file
2. **Specify the document type:** "This is a Policy Article / Overview / Known Gap / How-To"
3. **Answer clarifying questions** (one at a time)
4. **Choose output format:** HTML for ProProfs KB or DOCX for Word
5. **Download** the converted file

The Claude Project handles skill fetching and applies the appropriate structure automatically.

---

## Workflow Example

### Scenario: Converting `update-ring-groups.md` to a Word document

**Step 1:** You have a markdown file explaining SAW update ring groups. This is an Overview article (it explains a feature and design, not a single policy).

**Step 2:** Open the SAW Documentation Conversion Project in Claude app.

**Step 3:** Upload `update-ring-groups.md` and type:
> "This is an Overview article. Please convert it to DOCX format using the Overview skill."

**Step 4:** Claude fetches `SKILL-Overview.md` from the repository and asks:
> "Question 1: Is this overview for internal SAW team documentation, customer-facing documentation, or both?"

**Step 5:** You answer. Claude asks the next question (if needed).

**Step 6:** Once Claude has enough context, it converts the markdown into the Overview structure:
- Intro paragraph
- Documentation Confidence
- Properties table
- Purpose
- Feature Explanation
- How It Works
- Security Impact
- Known Impact table
- Related KB Articles
- Summary
- Microsoft Learn References

**Step 7:** Claude outputs a DOCX file ready for import into Word or ProProfs KB.

---

## Adding New Documentation

### Create a New Article

1. **Decide the document type** — Policy Article, Overview, Known Gap, How-To, or Description Field
2. **Review the corresponding skill file** in `/Skills/` (e.g., `SKILL-Policy-Article.md`)
3. **Write your `.md` file** following the skill's section structure and content rules
4. **Commit to your local documentation repository** (separate from this one)
5. **When ready to finalize**, upload to the Claude Project and specify the type
6. **Receive the converted HTML/DOCX** and import to ProProfs KB or Word

### Updating a Skill File

If you discover a gap or improvement to a skill file:

1. **Fork this repository** (if you don't have push access)
2. **Edit the skill file** in `/Skills/`
3. **Submit a pull request** with your changes
4. **Kenneth and Joost review** and merge
5. **The Claude Project automatically uses the updated skill** on the next conversion

---

## Content Standards

### Supported Platforms
Always mention supported platforms when describing compliance policies or features:
- Android device administrator
- Android AOSP
- Android Enterprise
- iOS/iPadOS
- Linux (Ubuntu Desktop 20.04 LTS and 22.04 LTS)
- macOS
- Windows 10/11

### Character Limits (if applicable)
- **Standard policy descriptions:** 1024 characters
- **M365 Apps policy descriptions:** 1000 characters
- **OEMConfig descriptions:** 260 characters (maximum)

### SAW Baseline Context
When documenting a policy, always explain:
- **Why SAW deploys this policy** — operational or security objective
- **Where it fits in the baseline** — foundational, hardening, compliance, etc.
- **Rollout strategy** — immediate deployment, staged via rings, or scenario-specific

---

## References & Resources

- **Microsoft Learn:** https://learn.microsoft.com/en-us/mem/intune/
- **Microsoft Entra ID documentation:** https://learn.microsoft.com/en-us/entra/identity/
- **Joost Gelijsteen (expert reference):** https://joostgelijsteen.com/
- **SAW website:** https://secureatwork.eu/

---

## Questions or Issues?

- **Skills unclear?** Review the specific skill file and the examples within it
- **Claude Project not working?** Check that the system prompt is configured correctly and the repository is accessible
- **Content rule conflicts?** The skill files are the source of truth; refer to the relevant skill for clarification

---

## License & Attribution

These documentation guidelines and skill files are part of the Secure at Work platform. They are maintained by Kenneth van Surksum and Joost Gelijsteen.

For questions or contributions, please open an issue or submit a pull request to this repository.

---

**Last updated:** June 2025

**Repository:** https://github.com/kennethvs/saw-documentation-guidelines
