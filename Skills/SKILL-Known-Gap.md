---
name: Known-Gap
description: "Skill for formally documenting a platform limitation or missing capability in Microsoft Intune that has been assessed and accepted by SAW. Includes risk classification, decision record, and conditions for revisiting the decision."
---

# Known Gap Skill

Use this skill when documenting a capability that does not exist in Intune, a platform-specific limitation that cannot be worked around, or a feature gap that has been formally assessed and accepted by SAW.

---

## Document Structure

Every Known Gap article contains these sections in order:

1. **Intro Paragraph**
2. **Documentation Confidence**
3. **Properties Table**
4. **Purpose**
5. **Gap Description**
6. **Risk Assessment**
7. **Known Impact Table**
8. **Decision**
9. **Summary**
10. **Microsoft Learn References**

---

## Section Rules

### Intro Paragraph
One paragraph. Must include:
- What capability or feature is missing
- Which platform or scenario is affected
- Why this gap matters to SAW
- The formal acceptance status

**Example:**
> Company Portal on shared Android Enterprise devices cannot rename devices after enrollment. This capability gap affects ZonMw's Beurslaptop scenario (shared kiosks), where device naming conventions require post-enrollment renaming based on deployment location. SAW has formally accepted this gap as a platform limitation and implemented a workaround using a provisioning package-based naming scheme prior to enrollment.

### Documentation Confidence
One of three values:
- **High** — gap verified against Microsoft Learn, support documentation, or confirmed via Microsoft support / Product Group
- **Medium** — gap inferred from multiple sources or observed in testing; Microsoft documentation consulted
- **Low** — gap based on limited testing or user reports; not independently verified with Microsoft

State what efforts were made to confirm the gap and any ongoing tracking.

### Properties Table
Always include:
| Property | Value |
|---|---|
| Gap Title | Exact name of the missing capability |
| Affected Platform | Windows 10/11, macOS, iOS, Android, etc. |
| Affected Scenario | Device type, feature category, use case |
| Severity | Low / Medium / High |
| Workaround Available | Yes / Partial / No |
| Microsoft Status | Documented limitation / Not acknowledged / Escalated |

Add rows as appropriate:
- Date Identified
- Last Reviewed
- Product Group Contact (if applicable)
- Related Feature Request (if submitted)

### Purpose
Two to three sentences covering:
- What capability is missing and why an organization would need it?
- Why is the absence of this capability a gap for SAW?
- What is the operational or compliance impact?

**Example:**
> Company Portal on Android Enterprise does not expose device renaming functionality. Organizations with shared device scenarios often require post-enrollment device naming based on physical location, purpose, or deployment cohort. Without programmatic or UI-based renaming post-enrollment, administrators must either accept auto-generated device names or use alternative provisioning methods. For SAW, this gap affects shared device deployments that require human-readable naming conventions.

### Gap Description
Replace "Settings Overview" with a structured description.

Cover:
- **What exists** — what capability IS available
- **What is missing** — what capability is NOT available
- **Where the gap was discovered** — product use, customer request, documentation review, etc.
- **What was evaluated** — list any workarounds, alternative approaches, or feature requests submitted

**Example:**
```
## Gap Description

What exists:
- Company Portal on Android Enterprise allows device enrollment and basic device management
- Device name is assigned automatically by Company Portal during enrollment (format: AndroidManagedDevice-XXXXXXXX)
- Device name cannot be changed via Company Portal UI after enrollment
- Microsoft Intune admin center allows renaming devices via a dedicated UI

What is missing:
- Company Portal does not expose the renaming UI to end users
- Device names assigned during enrollment cannot be overridden programmatically via Graph API without unenrolling the device
- No bulk renaming capability exists for Android Enterprise devices

Evaluated workarounds:
1. **Provisioning package with device naming** — Pre-enrollment provisioning package sets a custom device name before Company Portal enrollment. Workaround works; selected for ZonMw implementation.
2. **PowerShell/Graph API renaming post-enrollment** — Requires unenrollment and re-enrollment to change the name; unacceptable for production.
3. **Android Device Administration (legacy)** — Supports programmatic device renaming via MDM commands. Not viable due to Android AD deprecation and ZonMw's Android Enterprise requirement.
4. **Manual renaming in Intune admin center** — Feasible for small deployments; not scalable for hundreds of shared devices.
5. **Feature request to Microsoft** — Submitted to Microsoft Product Group in Q2 2025; no ETA provided.
```

### Risk Assessment
One paragraph covering:
- **Risk level** — Low / Medium / High
- **Reasoning** — why is the risk acceptable or unacceptable to SAW?
- **Compensating controls** — what mitigations reduce the risk?
- **Residual risk** — what remains even with workarounds in place?

**Example:**
> Risk level: **Medium**. The inability to programmatically rename devices post-enrollment is acceptable because the provisioning package-based workaround ensures devices receive human-readable names during the enrollment process, before user hand-off. The residual risk is that manual renaming in Intune remains necessary if deployment needs change post-enrollment; this is mitigated by clear operational runbooks and monitoring. If the workaround fails (provisioning package is not applied), devices would be deployed with auto-generated names, reducing administrator visibility but not impacting user functionality.

### Known Impact Table
Two columns: **Component** and **Impact**.

List every mitigation that was evaluated and its outcome:

| Mitigation / Workaround | Outcome |
|---|---|
| Provisioning package with device naming | **Selected**. Pre-enrollment package applies custom name during enrollment. Reliable and meets naming requirements. |
| PowerShell/Graph API renaming | **Not viable**. Requires device unenrollment; unacceptable for production. |
| Android Device Administration | **Not viable**. Legacy platform; Android Enterprise is required. |
| Manual renaming in Intune admin center | **Partial workaround**. Feasible for small batches; not scalable for hundreds of devices. |
| Feature request to Microsoft | **Pending**. Submitted Q2 2025; no ETA. Revisit annually. |

Also include operational impacts:
- Dependency on provisioning package delivery before enrollment
- Manual fallback required if provisioning fails
- Device naming conventions must be finalized before provisioning package creation
- No post-enrollment programmatic renaming option

### Decision
Create a new `<h2>` section with:
- **Decision** — Accept / Reject / Defer
- **Reasoning** — why this decision was made
- **Conditions for revisit** — when/how the decision will be re-evaluated
- **Date decided** — when the formal decision was made
- **Approved by** — who approved this decision in SAW

**Example:**
```
## Decision

**Accept the gap.** The missing Company Portal renaming capability is accepted as a platform limitation. The provisioning package-based workaround provides a reliable alternative that meets ZonMw's device naming requirements without requiring unenrollment or manual remediation at scale.

**Reasoning:** Android Enterprise's architecture does not expose device renaming to Company Portal, and Microsoft has not committed to adding this capability. The selected workaround is production-proven and integrates cleanly with SAW's shared device onboarding process.

**Conditions for revisit:**
1. Microsoft Product Group releases device renaming functionality in Company Portal (feature request tracking: [ticket ID])
2. Annual review in Q2 of each year to assess new Android Enterprise capabilities
3. If ZonMw deployment at a new site requires different naming conventions, re-evaluate provisioning package approach

**Decision date:** 30 June 2025

**Approved by:** Kenneth van Surksum (SAW Architecture)
```

### Summary
Two paragraphs:

**Paragraph 1:** Restate the gap, the risk assessment, and the formal decision. Confirm that the gap has been formally accepted and documented.

**Paragraph 2:** Operational notes including:
- How this decision affects day-to-day operations
- What teams need to be aware of this gap
- Links to related workaround documentation
- Schedule for reviewing the decision

**Example Paragraph 1:**
> The inability to rename Android Enterprise devices via Company Portal post-enrollment is a known platform limitation that does not have a native Microsoft solution. SAW has formally accepted this gap because the provisioning package-based pre-enrollment naming workaround is reliable, scalable, and meets the operational requirements of the ZonMw Beurslaptop scenario. The residual risk is mitigated by clear operational procedures and fallback documentation.

**Example Paragraph 2:**
> The provisioning package approach requires coordination between infrastructure and mobile device management teams to ensure packages are delivered and applied before user enrollment. All administrators managing ZonMw shared devices must be trained on the provisioning package workflow and the limitations of post-enrollment renaming. For full details on the provisioning package implementation, see [How-To: Deploy Device Naming via Provisioning Package](#). This decision will be revisited annually in Q2 or immediately if Microsoft releases device renaming capabilities in Company Portal. Any changes to ZonMw's device naming conventions require reassessment of the provisioning package configuration.

### Microsoft Learn References
One `<li>` per link. Prioritize official documentation that confirms the gap or documents the affected feature.

**Example:**
```html
<ul>
  <li><a href="https://learn.microsoft.com/en-us/mem/intune/apps/company-portal-app">Company Portal app for Android Enterprise</a></li>
  <li><a href="https://learn.microsoft.com/en-us/mem/intune/remote-actions/device-rename">Rename a device in Intune</a></li>
  <li><a href="https://learn.microsoft.com/en-us/mem/intune/enrollment/android-enrollment-overview">Android Enterprise enrollment overview</a></li>
</ul>
```

---

## Content Rules

**Accuracy first.** Verify the gap exists via:
- Microsoft Learn documentation
- Microsoft support or Product Group confirmation
- Testing in a production or staging environment
- Multiple reliable sources

**No speculation.** Do not claim a gap exists if it is untested or unverified.

**Fair assessment.** Evaluate all reasonable workarounds. Do not dismiss workarounds without testing or clear reasoning.

**Decision authority.** Only SAW leadership can formally accept a gap. State who approved the decision and when.

**No invented timelines.** Do not speculate on Microsoft delivery dates for features. If a feature request is pending, state "no ETA" and check official Microsoft sources.

---

## Questions to Ask Before Starting

1. Has this gap been verified against Microsoft Learn or Microsoft support documentation?
2. What workarounds or alternative approaches have been evaluated?
3. Who in SAW is the decision-maker for accepting or rejecting this gap?
4. When should this decision be revisited (e.g., annual review, tied to a feature request, or triggered by a specific event)?

---

## Output Formats

This skill supports conversion to:
- **HTML** — for ProProfs KB known gap documentation
- **DOCX** — for internal decision records or customer communication

Both formats use the same section structure above.
