# Feature Specification: Content Architect Subagent

**Feature Branch**: `001-content-architect-subagent`
**Created**: 2025-11-28
**Status**: Draft
**Input**: User description: "Create Content Architect Subagent that scaffolds complete Docusaurus textbook structure for Physical AI & Humanoid Robotics course. The subagent must read COURSE_CONTENT.md, generate directory structure for 3 chapters with 2 lessons each (total 6 lessons), create _category_.json files, update sidebars.ts, and generate lesson placeholder markdown files. Input: COURSE_CONTENT.md and chapter selection (chapters 1-3, lessons 1-2 per chapter). Output: Complete docs/docs/ directory structure with chapter folders, lesson files, category configs, and updated sidebar configuration. Must support future expansion to additional lessons and chapters."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Initial Structure Generation (Priority: P1)

As a textbook developer, I need to quickly scaffold the complete directory structure for the first 3 chapters (6 lessons total) of the Physical AI & Humanoid Robotics textbook so that I can immediately start writing content without manually creating folders and files.

**Why this priority**: This is the foundational capability - without it, no other functionality is possible. It unblocks all content creation work.

**Independent Test**: Can be fully tested by invoking the subagent with COURSE_CONTENT.md and verifying that all 6 lesson files, 3 chapter directories, category configurations, and updated sidebar are created correctly. Delivers immediate value by providing a ready-to-use textbook structure.

**Acceptance Scenarios**:

1. **Given** COURSE_CONTENT.md exists with course outline, **When** developer invokes Content Architect Subagent with chapter selection (1-3, lessons 1-2), **Then** three chapter directories are created (chapter-01-foundations/, chapter-02-ros2/, chapter-03-simulation/)

2. **Given** chapter directories are created, **When** subagent processes lesson mapping, **Then** exactly 2 lesson markdown files are created in each chapter directory with proper naming (lesson-01-*.md, lesson-02-*.md)

3. **Given** lesson files are created, **When** subagent completes, **Then** each chapter directory contains a valid _category_.json file with chapter title, position, and description

4. **Given** all files are created, **When** subagent finishes, **Then** docs/sidebars.ts is updated with all 3 chapters and 6 lessons properly linked

5. **Given** structure is generated, **When** developer runs Docusaurus, **Then** all chapters and lessons appear in the navigation sidebar without errors

---

### User Story 2 - Lesson Placeholder Quality (Priority: P2)

As a content writer, I need each generated lesson file to contain a standardized template with all required sections (Introduction, Learning Objectives, Key Concepts, Quiz, etc.) so that I can start filling in content immediately without remembering the template structure.

**Why this priority**: High-quality placeholders significantly speed up content creation and ensure consistency across all lessons.

**Independent Test**: Can be tested by inspecting generated lesson files and verifying they contain all mandatory sections with proper frontmatter. Delivers value by providing consistent, ready-to-fill templates.

**Acceptance Scenarios**:

1. **Given** a lesson file is generated, **When** developer opens it, **Then** frontmatter contains sidebar_position, title, and description fields

2. **Given** lesson placeholder is created, **When** developer reviews content, **Then** all mandatory sections are present: Introduction, Learning Objectives, Key Concepts, Hands-on Exercise, Quiz, Key Takeaways, Further Reading

3. **Given** lesson template is loaded, **When** developer views it, **Then** each section contains helpful placeholder text indicating what content should go there

---

### User Story 3 - Future Expansion Support (Priority: P3)

As a textbook developer, I need the ability to add lesson 3 to existing chapters (expanding from 2 to 3 lessons per chapter) and later add chapters 4-6 without breaking existing structure, so that content can grow incrementally without manual restructuring.

**Why this priority**: Important for long-term maintainability but not critical for initial launch. Can be addressed after initial 6 lessons are complete.

**Independent Test**: Can be tested by running subagent again with expanded selection (chapters 1-3, lessons 1-3) and verifying new lessons integrate seamlessly. Delivers value by enabling iterative content development.

**Acceptance Scenarios**:

1. **Given** initial 6 lessons exist, **When** developer invokes subagent with lesson 3 included, **Then** new lesson-03-*.md files are added to each chapter without overwriting existing lessons

2. **Given** chapters 1-3 exist, **When** developer invokes subagent with chapters 4-6 selected, **Then** new chapter directories are created and sidebar is updated to include both old and new chapters

3. **Given** structure is expanded, **When** developer builds Docusaurus, **Then** navigation reflects all chapters and lessons in correct order without duplication

---

### Edge Cases

- **What happens when COURSE_CONTENT.md is missing?**
  Subagent MUST fail gracefully with clear error message: "COURSE_CONTENT.md not found. Please ensure the file exists at project root."

- **What happens when chapter directory already exists?**
  Subagent MUST detect existing directories and only create missing lessons, preserving any existing content. Log warning: "Chapter directory exists, adding only new lessons."

- **What happens when sidebars.ts has custom modifications?**
  Subagent MUST preserve existing sidebar entries and only append new chapter sections. Do not overwrite manually added content.

- **What happens when lesson naming conflicts occur?**
  Subagent MUST use consistent slug naming (e.g., lesson-01-embodied-intelligence.md) and warn if file already exists, asking for confirmation to overwrite.

- **What happens when invoked outside docs/ directory?**
  Subagent MUST detect current working directory and verify docs/docs/ path exists before proceeding. Fail with error if Docusaurus structure not found.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Subagent MUST read COURSE_CONTENT.md and parse chapter titles, lesson titles, and weekly breakdown

- **FR-002**: Subagent MUST accept chapter selection parameter (e.g., "chapters 1-3, lessons 1-2") to determine scope of generation

- **FR-003**: Subagent MUST create chapter directories with consistent naming convention (chapter-##-slug-name/)

- **FR-004**: Subagent MUST generate lesson markdown files with frontmatter (sidebar_position, title, description)

- **FR-005**: Subagent MUST create _category_.json file for each chapter containing label, position, and collapsed state

- **FR-006**: Subagent MUST update docs/sidebars.ts with TypeScript syntax to include all generated chapters and lessons

- **FR-007**: Subagent MUST populate lesson placeholders with standardized template sections: Introduction, Learning Objectives, Key Concepts, Hands-on Exercise, Quiz, Key Takeaways, Further Reading

- **FR-008**: Subagent MUST detect existing files and avoid overwriting without user confirmation

- **FR-009**: Subagent MUST log all file creation operations (directories, files, config updates) for verification

- **FR-010**: Subagent MUST validate generated structure by checking file existence and Docusaurus compatibility before reporting success

- **FR-011**: Subagent MUST support idempotent execution - running multiple times with same parameters should not create duplicates

- **FR-012**: Subagent MUST preserve manual customizations in sidebars.ts when updating with new chapters

### Key Entities

- **Chapter**: Represents a major course module
  - Attributes: Number (1-6), Title, Directory slug, Lessons list
  - Maps to: A directory in docs/docs/ and an entry in sidebars.ts

- **Lesson**: Represents a specific topic within a chapter
  - Attributes: Number (1-4), Title, Filename slug, Learning objectives
  - Maps to: A markdown file within a chapter directory

- **Course Content**: Source of truth for textbook structure
  - Attributes: Modules, Weekly breakdowns, Learning outcomes
  - Sourced from: COURSE_CONTENT.md

- **Category Configuration**: Metadata for chapter organization
  - Attributes: Label, Position, Collapsed state, Link
  - Persisted in: _category_.json per chapter

- **Sidebar Configuration**: Navigation structure for Docusaurus
  - Attributes: Chapter categories, Lesson links, Ordering
  - Persisted in: docs/sidebars.ts

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Developer can invoke the subagent and have complete textbook structure generated in under 30 seconds

- **SC-002**: All 6 lesson files (chapters 1-3, lessons 1-2 each) are created with zero manual file creation

- **SC-003**: Running Docusaurus build after structure generation produces zero errors and all chapters/lessons appear in navigation

- **SC-004**: Generated lesson templates contain all 7 mandatory sections, reducing content writer setup time from 15 minutes per lesson to zero

- **SC-005**: Subagent can be re-run with expanded scope (adding lesson 3 to each chapter) without breaking existing structure, enabling incremental content development

- **SC-006**: 100% of file creation operations are logged and verifiable, providing full transparency of what was created

- **SC-007**: Sidebar configuration remains valid TypeScript with proper syntax, passing `npm run build` validation

## Assumptions

- **Assumption 1**: COURSE_CONTENT.md follows the documented structure with clear Module/Week delineations
- **Assumption 2**: Docusaurus project already initialized in docs/ directory
- **Assumption 3**: docs/sidebars.ts exists and follows TypeScript exports pattern
- **Assumption 4**: Chapter slugs are derived from chapter titles using kebab-case (lowercase, hyphens)
- **Assumption 5**: Lesson slugs are derived from lesson titles using kebab-case
- **Assumption 6**: Sidebar positions are numeric and sequential (1, 2, 3...)
- **Assumption 7**: _category_.json files use standard Docusaurus schema
- **Assumption 8**: Future expansion will maintain same structure pattern (chapters with lessons)
- **Assumption 9**: Subagent runs in project root directory or can resolve paths to docs/
- **Assumption 10**: User has read/write permissions for docs/ directory
