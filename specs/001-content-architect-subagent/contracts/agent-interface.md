# Agent Interface Contract: Content Architect

**Version**: 1.0.0
**Date**: 2025-11-28
**Purpose**: Define how to invoke the Content Architect Subagent

---

## Invocation

### Command
```bash
# From repository root
claude agent content-architect
```

### Prerequisites
- COURSE_CONTENT.md must exist at repository root
- docs/ directory must contain initialized Docusaurus project
- docs/docs/ directory must exist
- docs/sidebars.ts must exist

---

## Input Contract

### Required Inputs

**1. COURSE_CONTENT.md** (File at repository root)
- **Format**: Markdown
- **Content**: Course structure with Module/Week headers and lesson topics
- **Schema**: See data-model.md CourseContent entity
- **Example**:
```markdown
* **Module 1: The Robotic Nervous System (ROS 2)**
  * Focus: Middleware for robot control.
  * ROS 2 Nodes, Topics, and Services.
```

**2. Chapter Selection** (Interactive prompt during execution)
- **Format**: Natural language or structured format
- **Examples**:
  - "chapters 1-3, lessons 1-2"
  - "chapters 1 to 3, 2 lessons per chapter"
  - "first 3 chapters, first 2 lessons each"
- **Default**: If not provided, agent prompts user interactively

### Optional Inputs

**1. Existing Structure** (Files in docs/docs/)
- **Behavior**: Agent detects existing files and preserves them
- **Overwrite Policy**: Ask user for confirmation before overwriting

---

## Output Contract

### Success Output

**1. Directory Structure**
```text
docs/docs/
├── chapter-01-{slug}/
│   ├── _category_.json
│   ├── lesson-01-{slug}.md
│   └── lesson-02-{slug}.md
├── chapter-02-{slug}/
│   ├── _category_.json
│   ├── lesson-01-{slug}.md
│   └── lesson-02-{slug}.md
└── chapter-03-{slug}/
    ├── _category_.json
    ├── lesson-01-{slug}.md
    └── lesson-02-{slug}.md
```

**2. Updated Files**
- `docs/sidebars.ts` - Appended with new chapter categories

**3. Console Output**
```text
✅ Content Architect Subagent - Execution Summary

📁 Created Directories:
   - docs/docs/chapter-01-foundations/
   - docs/docs/chapter-02-ros2/
   - docs/docs/chapter-03-simulation/

📄 Created Files (9 total):
   - docs/docs/chapter-01-foundations/_category_.json
   - docs/docs/chapter-01-foundations/lesson-01-introduction-embodied-intelligence.md
   - docs/docs/chapter-01-foundations/lesson-02-robotics-landscape.md
   - docs/docs/chapter-02-ros2/_category_.json
   - docs/docs/chapter-02-ros2/lesson-01-architecture-core-concepts.md
   - docs/docs/chapter-02-ros2/lesson-02-nodes-topics-services.md
   - docs/docs/chapter-03-simulation/_category_.json
   - docs/docs/chapter-03-simulation/lesson-01-gazebo-environment.md
   - docs/docs/chapter-03-simulation/lesson-02-unity-visualization.md

🔄 Updated Files:
   - docs/sidebars.ts

✅ Validation:
   - All files exist: ✓
   - JSON syntax valid: ✓
   - TypeScript syntax valid: ✓
   - Docusaurus build test: ✓

⏱️  Execution time: 12.4 seconds
```

### Error Output

**Format**: Clear error messages with actionable guidance

**Examples**:
```text
❌ Error: COURSE_CONTENT.md not found
   → Please ensure COURSE_CONTENT.md exists at project root: /path/to/project/

❌ Error: docs/docs/ directory not found
   → Ensure Docusaurus is initialized. Run: cd docs && npm init docusaurus

❌ Error: Invalid chapter selection format
   → Expected format: "chapters 1-3, lessons 1-2"
   → You provided: "chapter one to three"

❌ Error: Permission denied writing to docs/docs/
   → Check file permissions: chmod -R u+w docs/docs/
```

---

## Behavioral Contract

### Idempotency (FR-011)

**Behavior**: Agent can be run multiple times with same parameters without creating duplicates

**Rules**:
1. If chapter directory exists:
   - Log warning: "Chapter {number} directory exists, skipping creation"
   - Proceed to lesson generation

2. If lesson file exists:
   - Prompt: "lesson-##-{slug}.md exists. Overwrite? (y/n)"
   - If yes: Overwrite file
   - If no: Skip file, continue to next

3. If _category_.json exists:
   - Prompt: "_category_.json exists. Overwrite? (y/n)"
   - If yes: Overwrite file
   - If no: Preserve existing file

4. If sidebar entry exists:
   - Detect duplicate category label
   - Log warning: "Sidebar already contains '{label}', skipping duplicate"
   - Do not append duplicate entry

### Preservation of Manual Edits (FR-012)

**Behavior**: Agent preserves manually added content in sidebars.ts

**Strategy**:
1. Parse existing sidebars.ts to AST
2. Identify tutorialSidebar array
3. Append new categories to end of array
4. Preserve all existing entries
5. Maintain formatting and comments

**Example**:
```typescript
// BEFORE (manual edit)
const sidebars: SidebarsConfig = {
  tutorialSidebar: [
    {
      type: 'doc',
      id: 'intro',
      label: 'Welcome',  // <- Manual entry
    },
  ],
};

// AFTER (agent preserves manual entry)
const sidebars: SidebarsConfig = {
  tutorialSidebar: [
    {
      type: 'doc',
      id: 'intro',
      label: 'Welcome',  // <- Preserved
    },
    {
      type: 'category',
      label: 'Chapter 1: Foundations',  // <- Added by agent
      items: [...],
    },
  ],
};
```

### Validation (FR-010)

**Behavior**: Agent validates generated structure before reporting success

**Validation Steps**:
1. **File Existence Check**
   - Verify all expected directories created
   - Verify all expected .md files created
   - Verify all _category_.json files created

2. **Syntax Validation**
   - Parse _category_.json files as JSON (must succeed)
   - Parse sidebars.ts as TypeScript (must succeed)
   - Parse lesson frontmatter as YAML (must succeed)

3. **Docusaurus Build Test**
   - Run: `cd docs && npm run build`
   - Verify exit code 0 (success)
   - If build fails, report error with build output

4. **Integrity Check**
   - Verify sidebar references point to existing lesson files
   - Verify _category_.json positions are sequential
   - Verify lesson sidebar_positions are unique within chapter

**Failure Handling**:
- If any validation fails, report error and list failed checks
- Do not report success if validation incomplete

---

## Performance Contract

### Execution Time (SC-001)

**Target**: <30 seconds for 6 lesson files (3 chapters × 2 lessons)

**Breakdown**:
- Parse COURSE_CONTENT.md: <1s
- Generate 3 chapter directories: <0.5s
- Generate 6 lesson files: <3s
- Update sidebars.ts: <1s
- Validation (file existence): <0.5s
- Validation (Docusaurus build): <20s (depends on npm)
- **Total estimated**: ~25s

**Timeout**: If execution exceeds 60 seconds, log warning but continue

### Resource Usage

**Disk Space**:
- Per lesson file: ~1-2 KB (template only)
- Per chapter: ~5-10 KB total (2 lessons + _category_.json)
- **Total for 6 lessons**: ~30 KB

**Memory**:
- Minimal (file operations only, no large data structures)

---

## Error Handling Contract

### Error Categories

**1. Input Errors** (User-correctable)
- COURSE_CONTENT.md missing
- Invalid chapter selection format
- Malformed COURSE_CONTENT.md

**2. Environment Errors** (Setup issues)
- docs/docs/ directory missing
- sidebars.ts missing
- Permission denied

**3. Validation Errors** (Output quality)
- JSON syntax invalid
- TypeScript syntax invalid
- Docusaurus build fails

**4. Runtime Errors** (Unexpected)
- Disk full
- File system errors
- Network errors (if fetching templates)

### Recovery Strategy

**Fail Fast**: Agent stops execution on first critical error

**Partial Success**: If some files created before error, list what was created and what failed

**Rollback**: Not implemented (user can manually delete created files)

**Example Partial Success Output**:
```text
⚠️  Partial Success - Execution stopped due to error

✅ Successfully Created:
   - docs/docs/chapter-01-foundations/ (with 2 lessons)
   - docs/docs/chapter-02-ros2/ (with 2 lessons)

❌ Failed:
   - docs/docs/chapter-03-simulation/ - Error: Permission denied

🔄 To retry: Fix permissions and run agent again (existing files will be preserved)
```

---

## Versioning Contract

### Agent Version

**Format**: Semantic versioning (MAJOR.MINOR.PATCH)
**Current**: 1.0.0

**Version Changes**:
- **MAJOR**: Breaking changes to input/output contract
- **MINOR**: New features, backward-compatible
- **PATCH**: Bug fixes, no interface changes

### Output Format Version

**Category JSON Version**: Docusaurus 3.x schema
**Sidebar TypeScript Version**: Docusaurus 3.x schema
**Lesson Frontmatter Version**: Docusaurus 3.x schema

**Compatibility**: Agent output is compatible with Docusaurus 3.0+

---

## Testing Contract

### Manual Testing

**Test Case 1: Fresh Scaffolding**
1. Delete docs/docs/ directory (if exists)
2. Recreate empty docs/docs/
3. Run agent with "chapters 1-3, lessons 1-2"
4. Verify all 6 lessons created
5. Verify Docusaurus build succeeds

**Test Case 2: Idempotent Re-run**
1. Run agent with same parameters twice
2. Verify no duplicate files created
3. Verify sidebar has no duplicate entries

**Test Case 3: Partial Expansion**
1. Run agent with "chapters 1-3, lessons 1-2"
2. Run agent again with "chapters 1-3, lessons 1-3"
3. Verify lesson-03-*.md files added to each chapter
4. Verify existing lesson-01 and lesson-02 files preserved

### Automated Testing

**Unit Tests**: See tasks.md (created by /sp.tasks)

**Integration Test**:
```bash
# Test full workflow
cd /path/to/project
rm -rf docs/docs/chapter-*
claude agent content-architect << EOF
chapters 1-3, lessons 1-2
EOF
cd docs && npm run build
# Expect: exit code 0
```

---

**Contract complete. This document defines the complete interface for invoking and interacting with the Content Architect Subagent.**
