---
name: Description-Field
description: "Skill for generating a single Intune Description field value in the exact pipe-delimited format used in the portal, with concise, verified SAW policy text and aligned category and version-change segments."
---

# Description Field Skill

Use this skill when you need to generate or improve a single Intune Description field value for a SAW policy. The output must visually match the portal style as one linear, pipe-delimited string.

---

## Output Structure

Every Description Field request should produce one final output only:

1. **Final Description Field**

---

## Final Description Field

The final output must be a single-line string that preserves the visible Intune Description field format.

Presentation requirement:
- Return the final description field inside one plain text canvas-style block, ready for copy paste
- Use a single fenced `text` code block containing only the final pipe-delimited string
- Do not add any text before or after the block unless the user explicitly asks for explanation

Fail-closed output contract:
- If any required input is missing, ask for it and stop
- Never output thought process, notes, diagnostics, issue flags, confidence commentary, or "copy-paste line" helper text
- Never output SHA/API fetch status inside the generated description content

Default structure:

```text
| What does this do? | <Description text> | Why should you use this? | <Description text> | What is the end-user impact? | <Impact text> | Learn more | [<Link description>](<Hyperlink address>) |
```

Compliance variant:

```text
| Category: | <Category text> | What does this do? | <Description text> | Why should you use this? | <Description text> | What is the end-user impact? | <Impact text> | Learn more | [<Link description>](<Hyperlink address>) |
```

Versioned variant:

```text
| What does this do? | <Description text> | Why should you use this? | <Description text> | What is the end-user impact? | <Impact text> | Learn more | [<Link description>](<Hyperlink address>) | Version <Major.Minor> | <Explanation on what changed> |
```

Compliance + versioned variant:

```text
| Category: | <Category text> | What does this do? | <Description text> | Why should you use this? | <Description text> | What is the end-user impact? | <Impact text> | Learn more | [<Link description>](<Hyperlink address>) | Version <Major.Minor> | <Explanation on what changed> |
```

Rules:
- If input is JSON, ask first: `Based on this JSON, what do you want me to generate?` and wait for confirmation
- Use a default maximum length of 1024 characters for the full description field output when no explicit limit is provided
- If a stricter platform-specific limit is known, apply that limit instead (for example M365 Apps 1000, OEMConfig 260)
- Output the final answer as one line unless the user explicitly asks for line breaks
- Preserve the pipe characters `|` exactly as visible separators
- Keep the core field order aligned across all variants: `Category:` if present, `What does this do?`, `Why should you use this?`, `What is the end-user impact?`, `Learn more`, and `Version <Major.Minor>` if present
- Do not convert the output into markdown headings, bullets, tables, HTML, JSON, YAML, or prose explanation
- Do not include analysis sections such as Context Summary, Constraints, Candidate Descriptions, Recommended Description, Template Output, or Validation Checklist unless the user explicitly asks for them
- Replace all placeholders with concrete text before final output
- Keep `What does this do?` to one concise sentence
- Keep `Why should you use this?` to one concise sentence
- Standardize impact wording to plain text: `Low Impact.`, `Medium Impact.`, or `High Impact.`
- Convert any HTML impact markup to plain text. Example: `<span style='color: green'>Low Impact.</span>` becomes `Low Impact.`
- Always include the `Learn more` field in the output
- The `Learn more` value must use markdown link format: `[<Link description>](<Hyperlink address>)`
- Use verified links only; if no verified link is available, use `TBD - link pending verification`
- Include `Category:` only when the policy type is meaningful and known, such as `Compliance policy`
- Include a version-change segment only when a version is present in the policy name or provided by the user
- When a version is present in the policy name or source, include `Version <Major.Minor> | <Explanation on what changed>` as part of the aligned field set
- Use the bare version value in the label, for example `Version 1.0`, not `Version v1.0`, unless the source explicitly requires the `v`
- The change explanation must be concise and specific to what changed
- If required author fields are null or missing (for example description text or version-change explanation), ask the user for the exact value before generating
- If a version is present but no change explanation is available, ask for the explanation and stop
- Do not infer missing author intent from Microsoft Learn unless the user explicitly approves inference
- Do not append version/change notes outside the pipe-delimited structure
- Do not leave unresolved placeholders such as `<>`

Source parsing rules:
- If the source already contains a pipe-delimited description, preserve the structure and only clean the content
- If the source contains HTML tags, strip them unless they are part of a verified markdown link label or URL
- If the source contains portal field labels and values in free text, normalize them into the approved pipe-delimited structure
- If the policy name contains a suffix such as `- v1.0`, extract the numeric version and format it as `Version 1.0`
- If the policy is clearly a compliance policy, prefer the compliance variant over the default variant
- If the user provides a prefilled six-part line in this order: `Category | What does this do | Why should you use this | End-user impact | Learn more link | Version/change`, map it directly to the canonical labeled output and do not add commentary
- If the user provides five parts (no version/change), map directly to the compliance or default labeled output based on whether Category is present and meaningful
- When direct mapping is possible from user-provided values, do not ask additional questions unless a required field is empty

---

## Content Rules

**Accuracy first.** Do not claim support for platforms, integrations, or workflows that are not explicitly confirmed.

**No fabrication.** Do not invent policy names, repositories, links, or automation capabilities.

**No vague fluff.** Keep language concrete and technical.

**Single responsibility.** The description should reflect one artifact's purpose, not a whole program unless explicitly requested.

**No em dashes.** Use commas or regular hyphens.

**Use exact terminology.** Prefer exact Microsoft and SAW terms such as Intune admin center, Microsoft Entra ID, Settings Catalog, and Properties Catalog.

---

## Questions to Ask Before Starting

Ask only if missing:

1. What is the exact policy name?
2. Based on this JSON, what do you want me to generate?
3. Is this a compliance policy, configuration profile, remediation, or another policy type?
4. Is there a verified Microsoft Learn link to include?
5. Should the version-change segment be included exactly as shown in the policy name?
6. What is the exact one-sentence explanation of what changed in this version?
7. Is there a stricter character limit than the default 1024 for this target policy type?

If these are already provided, proceed directly without repeating questions.

---

## Validation

Before returning the final string, confirm:

- The output is one pipe-delimited line
- The output is wrapped in a single `text` fenced block for copy paste
- The visible separators match the portal style
- The core fields appear in the canonical order
- The `Learn more` field is present and uses `[]()` markdown link syntax, unless a verified link is unavailable
- HTML has been removed
- Only verified content is present
- Optional segments are included only when justified by the source
- There is no extra text before or after the fenced block
- There are no notes, warnings, diagnostics, or helper labels in the final output
- Total output length does not exceed the active character limit (default 1024 unless a stricter limit applies)

If any check fails, revise once and return only the corrected final string.