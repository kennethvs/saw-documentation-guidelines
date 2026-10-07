# Skill Quality Gate

Use this checklist whenever a skill changes, especially for output formats such as Description Field.

## Required checks

1. One canonical output shape is documented.
2. One worked example of the final output exists.
3. The field order is explicit and unambiguous.
4. The allowed label set is explicit and complete.
5. Merge rules are explicit when multiple optional segments are present.
6. Trigger rules are explicit for category, version, and other optional sections.
7. Delimiter-safety rules are documented when pipe characters may appear in values.
8. The master system prompt defers to the skill file for format rules.
9. Every generation skill requires CEFR B2 (upper-intermediate) English for all generated prose and includes a B2 wording example and a review checklist.

## Writing-level checks

Write new skill instructions and examples at CEFR B2 level. When reviewing generated text, confirm:

- Wording is clear and direct, with familiar words, focused paragraphs, and manageable sentences.
- Necessary technical terms are kept and unfamiliar terms or acronyms are explained briefly where needed.
- Exact names, labels, values, commands, code, paths, and URLs remain unchanged.
- Simpler wording preserves conditions, risks, limitations, uncertainty, and all required technical details.
- Each skill's output structure and length limits are still met. Description Field output remains one line in one `text` block, with no extra prose.

## Minimum content for format-heavy skills

- A single canonical example of the final output
- A clear field-order section
- A clear label-list section
- A clear merge section for optional blocks
- A clear validation section
