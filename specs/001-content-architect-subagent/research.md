# Research: Content Architect Subagent

**Date**: 2025-11-28
**Feature**: Content Architect Subagent
**Purpose**: Document technical decisions for implementing the textbook scaffolding agent

---

## Research Questions & Findings

### 1. Claude Code Agent File Format

**Question**: How should a `.claude/agents/*.md` file be structured to define a subagent?

**Decision**: Use Markdown with YAML frontmatter and structured prompt sections

**Rationale**: Claude Code agents are defined as Markdown files with:
- YAML frontmatter specifying agent metadata (name, description, version)
- Prompt sections defining agent behavior and instructions
- Tool permissions (which Claude Code tools the agent can use)
- Input/output contracts

**Example Structure**:
```markdown
---
name: Content Architect
description: Scaffolds Docusaurus textbook structure
version: 1.0.0
tools:
  - Read
  - Write
  - Glob
  - Bash
---

# Content Architect Subagent

## Purpose
[Agent purpose and capabilities]

## Instructions
[Detailed step-by-step instructions for the agent]

## Inputs
[Expected inputs and their formats]

## Outputs
[Expected outputs and their locations]
```

**Alternatives Considered**:
- Python script: Rejected because it requires separate Claude SDK integration and doesn't leverage Claude Code's built-in tools
- Bash script: Rejected because it lacks AI-powered decision-making for handling edge cases

---

### 2. Docusaurus _category_.json Schema

**Question**: What is the correct schema for Docusaurus _category_.json files?

**Decision**: Use Docusaurus standard category configuration with label, position, and collapsed fields

**Rationale**: Docusaurus uses _category_.json to configure category behavior in the sidebar. The schema is:

```json
{
  "label": "Chapter Title",
  "position": 1,
  "collapsed": false,
  "link": {
    "type": "generated-index",
    "description": "Chapter description"
  }
}
```

**Fields**:
- `label` (required): Display name in sidebar
- `position` (required): Numeric ordering (1, 2, 3...)
- `collapsed` (optional): Whether category starts collapsed (default: true)
- `link` (optional): Generates an index page for the category

**Source**: Docusaurus documentation on sidebar categories

**Alternatives Considered**:
- Manual sidebar.ts entries only: Rejected because _category_.json provides better file-based configuration and auto-generation support

---

### 3. Docusaurus sidebars.ts TypeScript Syntax

**Question**: How should we programmatically update sidebars.ts without breaking TypeScript syntax?

**Decision**: Append new category objects to the tutorialSidebar array using template-based string manipulation

**Rationale**: Docusaurus sidebars.ts follows this pattern:

```typescript
import type {SidebarsConfig} from '@docusaurus/plugin-content-docs';

const sidebars: SidebarsConfig = {
  tutorialSidebar: [
    {
      type: 'category',
      label: 'Chapter 1: Foundations',
      items: [
        'chapter-01-foundations/lesson-01-embodied-intelligence',
        'chapter-01-foundations/lesson-02-robotics-landscape',
      ],
    },
    // More categories...
  ],
};

export default sidebars;
```

**Update Strategy**:
1. Read existing sidebars.ts
2. Parse to find the tutorialSidebar array closing bracket
3. Insert new category objects before the closing bracket
4. Preserve existing manually-added entries (FR-012)
5. Validate TypeScript syntax (trailing commas, proper brackets)

**Alternatives Considered**:
- Full AST parsing: Rejected as over-engineered for this use case
- Complete file replacement: Rejected because it violates FR-012 (preserve manual customizations)

---

### 4. Lesson File Frontmatter Format

**Question**: What YAML frontmatter should each lesson markdown file contain?

**Decision**: Use Docusaurus standard frontmatter with sidebar_position, title, and description

**Rationale**: Docusaurus lesson files require frontmatter for proper sidebar integration:

```yaml
---
sidebar_position: 1
title: Introduction to Embodied Intelligence
description: Foundations of Physical AI and embodied intelligence principles
---
```

**Fields**:
- `sidebar_position` (required): Numeric ordering within category (1, 2, 3...)
- `title` (required): Display name in sidebar and page header
- `description` (optional but recommended): Used for SEO and page metadata

**Source**: Docusaurus documentation on document metadata

**Alternatives Considered**:
- Additional custom fields (author, date, tags): Deferred to future enhancement (not in Phase 1 scope)

---

### 5. Slug Generation Algorithm

**Question**: How should chapter/lesson titles be converted to directory/file slugs?

**Decision**: Use kebab-case conversion (lowercase, hyphens, alphanumeric only)

**Rationale**: Docusaurus best practices recommend:
- Lowercase for URL consistency
- Hyphens for readability (not underscores)
- Remove special characters
- Preserve semantic meaning

**Algorithm**:
```
1. Convert to lowercase
2. Replace spaces with hyphens
3. Remove non-alphanumeric characters (except hyphens)
4. Remove consecutive hyphens
5. Trim leading/trailing hyphens
```

**Examples**:
- "Introduction to Embodied Intelligence" → "introduction-to-embodied-intelligence"
- "ROS 2 Architecture & Core Concepts" → "ros-2-architecture-core-concepts"
- "Gazebo Simulation Environment (Setup)" → "gazebo-simulation-environment-setup"

**Alternatives Considered**:
- snake_case: Rejected (less common in web URLs)
- Preserving case: Rejected (URLs should be case-insensitive)
- Numeric-only slugs: Rejected (loses semantic meaning)

---

### 6. COURSE_CONTENT.md Parsing Strategy

**Question**: How should the agent extract chapter and lesson information from COURSE_CONTENT.md?

**Decision**: Use section header parsing with fallback to Weekly Breakdown structure

**Rationale**: COURSE_CONTENT.md has two usable structures:

**Primary Source - Module Headers**:
```markdown
* **Module 1: The Robotic Nervous System (ROS 2)**
  * Focus: Middleware for robot control.
  * ROS 2 Nodes, Topics, and Services.
```

**Secondary Source - Weekly Breakdown**:
```markdown
### **Weeks 1-2: Introduction to Physical AI**
* Foundations of Physical AI and embodied intelligence
* From digital AI to robots that understand physical laws
```

**Parsing Strategy**:
1. Look for "Module #:" patterns to identify chapters
2. Extract bullet points under each module as lesson topics
3. Fall back to "Weeks #-#:" headers if modules not found
4. Map lessons to chapters based on chapter selection parameter (e.g., "chapters 1-3, lessons 1-2")

**Alternatives Considered**:
- Require structured JSON: Rejected (user preference for readable Markdown)
- Strict YAML frontmatter in COURSE_CONTENT.md: Rejected (adds complexity for content authors)

---

### 7. Lesson Template Structure

**Question**: What sections should the standardized lesson template contain?

**Decision**: Include 7 mandatory sections per FR-007 from spec

**Rationale**: Each lesson placeholder should guide content writers with clear structure:

```markdown
---
sidebar_position: 1
title: [Lesson Title]
description: [Lesson Description]
---

# [Lesson Title]

## Introduction
[Overview of the topic and its relevance]

## Learning Objectives
By the end of this lesson, you will be able to:
- [Objective 1]
- [Objective 2]
- [Objective 3]

## Key Concepts

### Concept 1
[Explanation]

### Concept 2
[Explanation]

## Hands-on Exercise
[Practical exercise or tutorial]

## Quiz
1. [Question 1]
2. [Question 2]
3. [Question 3]

## Key Takeaways
- [Takeaway 1]
- [Takeaway 2]
- [Takeaway 3]

## Further Reading
- [Resource 1]
- [Resource 2]
- [Resource 3]
```

**Source**: Spec requirement FR-007

**Alternatives Considered**:
- Minimal template: Rejected (doesn't provide enough guidance for content writers)
- Extended template with code blocks: Deferred to Technical Writer Subagent

---

### 8. Idempotency Strategy

**Question**: How should the agent handle re-execution without creating duplicates?

**Decision**: Check for existing files before creation and skip if present

**Rationale**: FR-011 requires idempotent execution. Strategy:

1. **Directory Check**: If chapter directory exists, log warning and skip creation
2. **File Check**: If lesson file exists, ask user for confirmation to overwrite (FR-008)
3. **Sidebar Check**: Parse existing sidebars.ts to avoid duplicate category entries
4. **_category_.json Check**: If exists, preserve and only update if user confirms

**Edge Case Handling**:
- Partial completion (some files exist, others don't): Create only missing files
- Version conflicts (file changed since last run): Warn user and require confirmation
- Sidebar drift (manual edits): Preserve manual entries, append new ones

**Alternatives Considered**:
- Force overwrite: Rejected (violates FR-008)
- Never overwrite: Rejected (limits flexibility for fixing mistakes)

---

### 9. Validation Strategy

**Question**: How should the agent verify generated structure is valid?

**Decision**: Multi-level validation with file existence checks and Docusaurus build test

**Rationale**: FR-010 requires validation before reporting success:

**Level 1 - File Existence**:
- Verify all expected directories created
- Verify all expected .md files created
- Verify all _category_.json files created

**Level 2 - Content Validation**:
- Check frontmatter contains required fields
- Verify all template sections present
- Validate JSON syntax in _category_.json

**Level 3 - Docusaurus Integration**:
- Run `npm run build` in docs/ directory
- Verify build succeeds (exit code 0)
- Check for TypeScript errors in sidebars.ts

**Logging**: FR-009 requires logging all operations for verification

**Alternatives Considered**:
- Manual inspection only: Rejected (not scalable, error-prone)
- Unit tests: Deferred to /sp.tasks phase

---

### 10. Error Handling Strategy

**Question**: How should the agent handle failures gracefully?

**Decision**: Fail fast with clear error messages per edge cases in spec

**Rationale**: Edge cases from spec:

| Scenario | Handling |
|----------|----------|
| COURSE_CONTENT.md missing | Fail with error: "COURSE_CONTENT.md not found. Please ensure the file exists at project root." |
| docs/docs/ not found | Fail with error: "Docusaurus structure not found. Ensure docs/docs/ directory exists." |
| Permission denied | Fail with error: "Cannot write to docs/ directory. Check file permissions." |
| Invalid chapter selection | Fail with error: "Invalid chapter selection format. Expected: 'chapters 1-3, lessons 1-2'" |
| Malformed COURSE_CONTENT.md | Fail with error: "Cannot parse COURSE_CONTENT.md. Ensure Module/Week headers are present." |

**Alternatives Considered**:
- Silent failure: Rejected (hides problems from user)
- Partial success: Rejected (leaves incomplete structure)

---

## Summary of Decisions

| Topic | Decision | Rationale |
|-------|----------|-----------|
| Agent Format | Markdown with YAML frontmatter | Standard Claude Code agent structure |
| _category_.json Schema | Docusaurus standard (label, position, collapsed) | Official Docusaurus format |
| sidebars.ts Updates | Template-based string insertion | Preserves manual edits, avoids AST complexity |
| Lesson Frontmatter | sidebar_position, title, description | Docusaurus required fields |
| Slug Generation | kebab-case conversion | Web URL best practices |
| Content Parsing | Module header extraction with Weekly fallback | Handles both content structures |
| Lesson Template | 7 mandatory sections | Per FR-007 specification |
| Idempotency | File existence checks + user confirmation | Satisfies FR-011 and FR-008 |
| Validation | Multi-level (files, content, build) | Per FR-010 requirement |
| Error Handling | Fail fast with clear messages | User-friendly troubleshooting |

---

**All research complete. Ready to proceed to Phase 1 (Design).**
