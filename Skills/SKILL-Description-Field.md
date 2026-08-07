---
name: Description-Field
description: "Skill for generating clear, accurate description fields for SAW documentation metadata, KB previews, and skill frontmatter. Produces concise, audience-aware descriptions with strict quality and length checks."
---

# Description Field Skill

Use this skill when you need to generate or improve a description field for a SAW documentation artifact, such as frontmatter metadata, KB previews, skill summaries, or reference catalog entries.

---

## Document Structure

Every Description Field request should produce these sections in order:

1. **Context Summary**
2. **Constraints**
3. **Candidate Descriptions**
4. **Recommended Description**
5. **Template Output**
6. **Validation Checklist**

---

## Section Rules

### Context Summary
One short paragraph describing:
- What artifact the description belongs to
- Intended audience
- Primary function of the artifact
- Any mandatory terminology

If context is incomplete, state what is missing before generating final output.

### Constraints
List explicit constraints before writing candidates.

Always evaluate at least:
- **Length target**: 140-220 characters by default unless specified otherwise
- **Voice**: neutral, operational, and specific
- **Scope**: what the artifact does, where it applies, and why it matters
- **Accuracy**: no unverified features, no speculative claims
- **Terminology**: exact portal/product names (Intune admin center, Microsoft Entra ID, Settings Catalog, Properties Catalog)

If a platform or audience is specified, include it in the description.

### Candidate Descriptions
Generate exactly 3 candidate descriptions.

Rules:
- Each candidate must be one sentence
- Each candidate must be meaningfully different in focus
- Avoid filler terms such as "comprehensive", "best-in-class", or "seamless"
- Avoid future promises or roadmap language
- Avoid repeated sentence openings across candidates

Recommended focus split:
1. Scope-first candidate (what it covers)
2. Outcome-first candidate (what it enables)
3. Operations-first candidate (how it is used)

### Recommended Description
Select one candidate as the recommendation.

Provide:
- Final sentence only (no label text in the sentence itself)
- Character count in parentheses after the sentence
- One-line rationale explaining why it best matches the constraints

Example format:
```
Skill for documenting a single Intune policy with verified settings, security impact, known impacts, and references in SAW KB format. (143 chars)
Rationale: Most precise coverage of scope and output without speculative language.
```

### Template Output
The final output must use one of these approved structures.

Default (preferred for metadata consistency):

```markdown
| What does this do? | <Description text> |
| Why should you use this? | <Description text> |
| What is the end-user impact? | <Description text> |
| Learn more | [<Link description>](<Hyperlink address>) |
```

Alternative (allowed when a KB or customer format explicitly asks for it):

```markdown
**Category:**
<Category text>
**What does this do?**
<Description text>
**Why should you use this?**
<Description text>
**What is the end-user impact?**
<Impact text>
**Learn more**
[<Link description>](<Hyperlink address>)
```

Compliance variant (use when Category is a compliance policy):

```markdown
**Category:**
<Category text>
**What does this do?**
<Description text>
**Why should you use this?**
<Description text>
**When do we mark device noncompliant?**
<Noncompliance rule text>
**What is the end-user impact?**
<Impact text>
**Learn more**
[<Link description>](<Hyperlink address>)
```

Versioned policy variant (use when version/change note is provided):

```markdown
**What does this do?**
<Description text>
**Why should you use this?**
<Description text>
**What is the end-user impact?**
<Impact text>
**Learn more**
[<Link description>](<Hyperlink address>)
**Version <Major.Minor>**
<Change summary>
```

Rules:
- Replace all placeholders with concrete text before final output
- Keep each description text to one to two sentences
- Use verified links only; if no verified link is available, use `TBD - link pending verification`
- Align wording with the selected Recommended Description
- In the alternative format, include `Category` only when the source asks for classification (for example, Compliance policy)
- In the compliance variant, `When do we mark device noncompliant?` is required and must include the configured action and timeline (for example, `Mark device noncompliant - 60 days`)
- In the versioned policy variant, `Version <Major.Minor>` is required when a change note is supplied
- Keep the version change summary to one concise sentence focused on what changed in behavior or targeting
- Standardize end-user impact wording to one of: `Low Impact.`, `Medium Impact.`, `High Impact.`
- For HTML-capable outputs, impact text may use color labels:
	- Low: `<span style='color: green'>Low Impact.</span>`
	- Medium: `<span style='color: orange'>Medium Impact.</span>`
	- High: `<span style='color: red'>High Impact.</span>`
- If end-user impact includes consequences, keep them explicit and concise (for example, `User can lose access if device falls out of compliance`)
- Do not leave unresolved placeholders such as `<>`

### Validation Checklist
Return a simple pass/fail checklist:

- Uses exact SAW and Microsoft terminology
- States scope and practical purpose
- Avoids unverifiable claims
- Meets length target
- Works as standalone metadata text

If any item fails, revise once and provide the corrected recommendation.

---

## Content Rules

**Accuracy first.** Do not claim support for platforms, integrations, or workflows that are not explicitly confirmed.

**No fabrication.** Do not invent policy names, repositories, links, or automation capabilities.

**No vague fluff.** Keep language concrete and technical.

**Single responsibility.** The description should reflect one artifact's purpose, not a whole program unless explicitly requested.

**No em dashes.** Use commas or regular hyphens.

---

## Questions to Ask Before Starting

Ask only if missing:

1. What artifact is this description for (skill file, KB article, runbook, or metadata entry)?
2. Who is the audience (internal SAW team, customer-facing, or both)?
3. Is there a strict length limit for the description field?
4. Are there mandatory terms that must appear?

If these are already provided, proceed directly without repeating questions.

---

## Output Formats

This skill supports conversion to:
- **Markdown text output** - for frontmatter, templates, and source-controlled metadata
- **HTML snippet** - for KB systems that need inline summary text

Both formats use the same generation and validation rules above.