# Quickstart: Content Architect Subagent

**Version**: 1.0.0
**Date**: 2025-11-28
**Estimated Time**: 5 minutes

---

## What is the Content Architect Subagent?

The Content Architect Subagent automates the tedious process of creating directory structures, configuration files, and lesson placeholders for Docusaurus textbooks. Instead of manually creating folders and files, you invoke the agent and it scaffolds the entire structure in seconds.

**What it does**:
- ✅ Creates chapter directories with proper naming
- ✅ Generates lesson markdown files with standardized templates
- ✅ Creates `_category_.json` configuration for each chapter
- ✅ Updates `docs/sidebars.ts` with new chapters
- ✅ Validates output for Docusaurus compatibility

**What it doesn't do**:
- ❌ Write actual lesson content (that's the Technical Writer Subagent's job)
- ❌ Create diagrams or images
- ❌ Generate quizzes with real questions

---

## Prerequisites

Before using the Content Architect Subagent, ensure you have:

1. **COURSE_CONTENT.md** at repository root
   - Contains course outline with Module/Week structure
   - See [COURSE_CONTENT.md](../../COURSE_CONTENT.md) for example

2. **Docusaurus Project** initialized in `docs/` directory
   ```bash
   cd docs && npm install
   ```

3. **Claude Code** installed and configured
   ```bash
   claude --version  # Should show Claude Code version
   ```

4. **Write Permissions** for `docs/docs/` directory
   ```bash
   ls -ld docs/docs/  # Should show write permissions
   ```

---

## Quick Start (5 Steps)

### Step 1: Navigate to Project Root

```bash
cd /path/to/add-hackathon-2k25
```

### Step 2: Verify COURSE_CONTENT.md Exists

```bash
cat COURSE_CONTENT.md | head -20
```

You should see Module headers like:
```markdown
* **Module 1: The Robotic Nervous System (ROS 2)**
  * Focus: Middleware for robot control.
```

### Step 3: Invoke the Content Architect Agent

```bash
claude agent content-architect
```

**Alternative** (if agent is registered as slash command):
```bash
# Inside Claude Code interactive session
/content-architect
```

### Step 4: Provide Chapter Selection

The agent will prompt:
```
Content Architect Subagent: How many chapters and lessons should I generate?
```

Respond with:
```
chapters 1-3, lessons 1-2
```

**Other valid formats**:
- "chapters 1 to 3, 2 lessons per chapter"
- "first 3 chapters, first 2 lessons each"
- "3 chapters, 2 lessons"

### Step 5: Review Generated Structure

The agent will output:
```text
✅ Content Architect Subagent - Execution Summary

📁 Created Directories (3):
   - docs/docs/chapter-01-foundations/
   - docs/docs/chapter-02-ros2/
   - docs/docs/chapter-03-simulation/

📄 Created Files (9):
   [List of all created files]

🔄 Updated Files:
   - docs/sidebars.ts

✅ Validation Passed
⏱️  Execution time: 12.4 seconds
```

### Step 6: Verify in Docusaurus

```bash
cd docs && npm run start
```

Visit http://localhost:3000 and verify:
- All 3 chapters appear in sidebar
- Each chapter has 2 lessons
- Clicking lessons shows placeholder content

---

## Usage Examples

### Example 1: Initial Scaffolding (Phase 1)

**Goal**: Generate structure for first 3 chapters, 2 lessons each

**Command**:
```bash
claude agent content-architect
```

**Input**:
```
chapters 1-3, lessons 1-2
```

**Result**: 6 lesson files created (3 chapters × 2 lessons)

---

### Example 2: Adding More Lessons

**Goal**: Expand existing chapters from 2 to 3 lessons each

**Command**:
```bash
claude agent content-architect
```

**Input**:
```
chapters 1-3, lessons 1-3
```

**Result**:
- Existing lessons 1-2 preserved
- New lesson 3 added to each chapter
- Total: 9 lesson files (3 chapters × 3 lessons)

---

### Example 3: Adding More Chapters

**Goal**: Add chapters 4-6 to existing 1-3

**Command**:
```bash
claude agent content-architect
```

**Input**:
```
chapters 1-6, lessons 1-2
```

**Result**:
- Existing chapters 1-3 preserved
- New chapters 4-6 created
- Total: 12 lesson files (6 chapters × 2 lessons)

---

## Understanding the Output

### Directory Structure

```text
docs/docs/
├── chapter-01-foundations/          ← Chapter directory
│   ├── _category_.json              ← Chapter config (label, position)
│   ├── lesson-01-*.md               ← Lesson 1 placeholder
│   └── lesson-02-*.md               ← Lesson 2 placeholder
├── chapter-02-ros2/
│   ├── _category_.json
│   ├── lesson-01-*.md
│   └── lesson-02-*.md
└── chapter-03-simulation/
    ├── _category_.json
    ├── lesson-01-*.md
    └── lesson-02-*.md
```

### Lesson File Structure

Each lesson file contains:

```markdown
---
sidebar_position: 1
title: Introduction to Embodied Intelligence
description: Foundations of Physical AI principles
---

# Introduction to Embodied Intelligence

## Introduction
[Placeholder text]

## Learning Objectives
[Placeholder text]

## Key Concepts
[Placeholder text]

## Hands-on Exercise
[Placeholder text]

## Quiz
[Placeholder text]

## Key Takeaways
[Placeholder text]

## Further Reading
[Placeholder text]
```

**Next Step**: Use Technical Writer Subagent to fill in actual content.

---

## Common Issues & Solutions

### Issue 1: "COURSE_CONTENT.md not found"

**Cause**: File missing from repository root

**Solution**:
```bash
# Check current directory
pwd

# Verify file exists
ls -la COURSE_CONTENT.md

# If missing, create it or check you're in the right directory
cd /path/to/add-hackathon-2k25
```

---

### Issue 2: "docs/docs/ directory not found"

**Cause**: Docusaurus not initialized

**Solution**:
```bash
# Initialize Docusaurus
cd docs
npm init docusaurus

# Or create directory manually
mkdir -p docs/docs
```

---

### Issue 3: "Permission denied writing to docs/docs/"

**Cause**: Insufficient file permissions

**Solution**:
```bash
# Grant write permissions
chmod -R u+w docs/docs/

# Or use sudo (if necessary)
sudo chown -R $USER:$USER docs/docs/
```

---

### Issue 4: "Docusaurus build failed"

**Cause**: Invalid TypeScript syntax in sidebars.ts or missing lesson files

**Solution**:
```bash
# Check TypeScript syntax
npx tsc --noEmit docs/sidebars.ts

# View detailed build errors
cd docs && npm run build

# Common fix: Ensure all document IDs in sidebar reference existing files
```

---

### Issue 5: Agent creates duplicate chapters

**Cause**: Running agent twice with same parameters

**Solution**: This is expected behavior (idempotent execution). Agent will detect existing chapters and log warnings but not create duplicates.

---

## Advanced Usage

### Custom Chapter Selection

**Scenario**: Only want chapters 1 and 3 (skip 2)

**Solution**: Run agent twice:
```bash
# First run: Chapter 1
claude agent content-architect
> chapters 1, lessons 1-2

# Second run: Chapter 3
claude agent content-architect
> chapters 3, lessons 1-2
```

---

### Overwriting Existing Files

**Scenario**: Need to regenerate lesson templates after modifying COURSE_CONTENT.md

**Solution**: Agent will prompt for confirmation:
```
lesson-01-intro.md already exists. Overwrite? (y/n)
```

Type `y` to overwrite, `n` to skip.

---

### Validating Output Manually

**Scenario**: Want to verify structure without building Docusaurus

**Solution**:
```bash
# Check all files created
find docs/docs/chapter-* -type f

# Validate JSON syntax
for f in docs/docs/chapter-*/_category_.json; do
  echo "Checking $f..."
  jq . "$f" > /dev/null && echo "✓ Valid" || echo "✗ Invalid"
done

# Validate TypeScript syntax
npx tsc --noEmit docs/sidebars.ts
```

---

## Next Steps

After Content Architect completes:

1. **Verify Structure**: Run Docusaurus locally to inspect generated files
   ```bash
   cd docs && npm run start
   ```

2. **Fill in Content**: Use Technical Writer Subagent to replace placeholders
   ```bash
   claude agent technical-writer
   ```

3. **Review Content**: Manually review generated content for accuracy

4. **Build for Production**: Deploy to GitHub Pages
   ```bash
   cd docs && npm run build
   ```

---

## Performance Notes

**Execution Time**:
- 6 lesson files (3 chapters × 2 lessons): ~10-15 seconds
- 12 lesson files (6 chapters × 2 lessons): ~20-30 seconds
- 24 lesson files (6 chapters × 4 lessons): ~40-60 seconds

**Bottleneck**: Docusaurus build validation (takes 15-20 seconds)

**Skip Validation** (if needed for speed):
```bash
# Run agent without build validation (not recommended)
# This option would be added in future version
```

---

## Troubleshooting Checklist

Before reporting an issue, verify:

- [ ] COURSE_CONTENT.md exists at repository root
- [ ] docs/docs/ directory exists
- [ ] docs/sidebars.ts exists
- [ ] You have write permissions to docs/docs/
- [ ] Node.js and npm are installed (`node --version`)
- [ ] Docusaurus dependencies installed (`npm install` in docs/)
- [ ] No file system errors (disk full, etc.)

---

## Getting Help

**View Agent Logs**:
```bash
# Check Claude Code logs for detailed execution trace
claude logs --last
```

**Report Issues**:
- GitHub Issues: https://github.com/NaimalArain13/add-hackathon-2k25/issues
- Include: Error message, COURSE_CONTENT.md snippet, system info

**Contribute**:
- Submit improvements to agent specification
- Add new features via PRs

---

## Related Documentation

- [Agent Interface Contract](./contracts/agent-interface.md) - Full API specification
- [Data Model](./data-model.md) - Entity definitions
- [Lesson Template](./contracts/lesson-template.md) - Template structure
- [Sidebar Update Rules](./contracts/sidebar-update-rules.md) - How sidebar is modified

---

**You're ready to use the Content Architect Subagent! Run `claude agent content-architect` to get started.**
