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

## Minimum content for format-heavy skills

- A single canonical example of the final output
- A clear field-order section
- A clear label-list section
- A clear merge section for optional blocks
- A clear validation section
