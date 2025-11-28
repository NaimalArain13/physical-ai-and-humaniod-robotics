# Data Model: Content Architect Subagent

**Date**: 2025-11-28
**Feature**: Content Architect Subagent
**Purpose**: Define entities, attributes, relationships, and persistence for textbook scaffolding

---

## Entity Definitions

### 1. CourseContent

**Description**: Source of truth for textbook structure, parsed from COURSE_CONTENT.md

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `modules` | Module[] | Yes | List of course modules (chapters) |
| `weeks` | Week[] | No | Weekly breakdown structure (fallback) |
| `learning_outcomes` | string[] | No | Course-level learning outcomes |
| `hardware_requirements` | string | No | Hardware requirements section |

**Relationships**:
- Contains many `Module` entities
- Contains many `Week` entities

**Persistence**:
- **Source**: COURSE_CONTENT.md (Markdown file at repository root)
- **Access**: Read-only (agent does not modify this file)

**Validation Rules**:
- Must contain at least 1 Module or Week entry
- Module headers must match pattern: "Module #: Title"
- Week headers must match pattern: "Weeks #-#: Title"

**Example**:
```markdown
* **Module 1: The Robotic Nervous System (ROS 2)**
  * Focus: Middleware for robot control.
  * ROS 2 Nodes, Topics, and Services.

### **Weeks 1-2: Introduction to Physical AI**
* Foundations of Physical AI and embodied intelligence
```

---

### 2. Module (Chapter)

**Description**: Represents a major course module, maps to a chapter in the textbook

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `number` | integer | Yes | Module number (1-6) |
| `title` | string | Yes | Full module title |
| `slug` | string | Yes | Directory-safe slug (kebab-case) |
| `focus` | string | No | Focus area description |
| `topics` | string[] | Yes | List of lesson topics within module |
| `directory` | string | Yes | Generated directory path |

**Relationships**:
- Belongs to one `CourseContent`
- Contains many `Lesson` entities

**Persistence**:
- **Directory**: `docs/docs/chapter-{number:02d}-{slug}/`
- **Config**: `docs/docs/chapter-{number:02d}-{slug}/_category_.json`

**Validation Rules**:
- Number must be 1-6
- Title must not be empty
- Slug must match pattern: `^[a-z0-9]+(-[a-z0-9]+)*$`
- Must contain at least 1 topic/lesson

**Slug Generation**:
```
"The Robotic Nervous System (ROS 2)"
  → "the-robotic-nervous-system-ros-2"

"AI-Robot Brain (NVIDIA Isaac™)"
  → "ai-robot-brain-nvidia-isaac"
```

**Example**:
```json
{
  "number": 1,
  "title": "The Robotic Nervous System (ROS 2)",
  "slug": "robotic-nervous-system-ros-2",
  "focus": "Middleware for robot control",
  "topics": [
    "ROS 2 Nodes, Topics, and Services",
    "Bridging Python Agents to ROS controllers"
  ],
  "directory": "docs/docs/chapter-01-robotic-nervous-system-ros-2/"
}
```

---

### 3. Lesson

**Description**: Represents a specific topic within a chapter, maps to a lesson markdown file

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `number` | integer | Yes | Lesson number within chapter (1-4) |
| `title` | string | Yes | Lesson title |
| `slug` | string | Yes | Filename slug (kebab-case) |
| `description` | string | Yes | Brief lesson description |
| `chapter_number` | integer | Yes | Parent chapter number |
| `sidebar_position` | integer | Yes | Order in sidebar (1, 2, 3...) |
| `content_sections` | string[] | Yes | Template sections to include |
| `filepath` | string | Yes | Generated file path |

**Relationships**:
- Belongs to one `Module` (Chapter)
- References one `LessonTemplate`

**Persistence**:
- **File**: `docs/docs/chapter-{chapter:02d}-{chapter_slug}/lesson-{number:02d}-{lesson_slug}.md`
- **Frontmatter**: YAML header in the markdown file

**Validation Rules**:
- Number must be 1-4
- Title must not be empty
- Slug must match pattern: `^[a-z0-9]+(-[a-z0-9]+)*$`
- Filepath must be unique within the textbook
- All content_sections must exist in LessonTemplate

**Frontmatter Schema**:
```yaml
---
sidebar_position: 1
title: "Introduction to Embodied Intelligence"
description: "Foundations of Physical AI and embodied intelligence principles"
---
```

**Example**:
```json
{
  "number": 1,
  "title": "Introduction to Embodied Intelligence",
  "slug": "introduction-embodied-intelligence",
  "description": "Foundations of Physical AI and embodied intelligence principles",
  "chapter_number": 1,
  "sidebar_position": 1,
  "content_sections": [
    "Introduction",
    "Learning Objectives",
    "Key Concepts",
    "Hands-on Exercise",
    "Quiz",
    "Key Takeaways",
    "Further Reading"
  ],
  "filepath": "docs/docs/chapter-01-foundations/lesson-01-introduction-embodied-intelligence.md"
}
```

---

### 4. CategoryConfiguration

**Description**: Docusaurus category metadata for chapter organization

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `label` | string | Yes | Display name in sidebar |
| `position` | integer | Yes | Numeric ordering (1, 2, 3...) |
| `collapsed` | boolean | No | Start collapsed? (default: false) |
| `link_type` | string | No | Link type (default: "generated-index") |
| `description` | string | No | Category description |

**Relationships**:
- Belongs to one `Module` (Chapter)

**Persistence**:
- **File**: `docs/docs/chapter-{number:02d}-{slug}/_category_.json`
- **Format**: JSON

**Validation Rules**:
- Label must not be empty
- Position must be positive integer
- Link type must be one of: "generated-index", "doc", null

**JSON Schema**:
```json
{
  "label": "Chapter 1: Foundations",
  "position": 1,
  "collapsed": false,
  "link": {
    "type": "generated-index",
    "description": "Introduction to Physical AI and embodied intelligence"
  }
}
```

**Example**:
```json
{
  "label": "Chapter 1: Foundations",
  "position": 1,
  "collapsed": false,
  "link_type": "generated-index",
  "description": "Introduction to Physical AI and embodied intelligence"
}
```

---

### 5. SidebarConfiguration

**Description**: Docusaurus sidebar navigation structure

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `sidebar_name` | string | Yes | Sidebar identifier (e.g., "tutorialSidebar") |
| `categories` | SidebarCategory[] | Yes | List of category entries |

**Relationships**:
- Contains many `SidebarCategory` entries

**Persistence**:
- **File**: `docs/sidebars.ts`
- **Format**: TypeScript module export

**Validation Rules**:
- Must be valid TypeScript syntax
- Sidebar name must match Docusaurus config
- Categories must be properly nested with valid item paths

**TypeScript Structure**:
```typescript
import type {SidebarsConfig} from '@docusaurus/plugin-content-docs';

const sidebars: SidebarsConfig = {
  tutorialSidebar: [
    {
      type: 'category',
      label: 'Chapter 1: Foundations',
      items: [
        'chapter-01-foundations/lesson-01-introduction-embodied-intelligence',
        'chapter-01-foundations/lesson-02-robotics-landscape',
      ],
    },
    // More categories...
  ],
};

export default sidebars;
```

---

### 6. SidebarCategory

**Description**: Individual category entry in the sidebar

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | string | Yes | Always "category" for chapters |
| `label` | string | Yes | Display name in sidebar |
| `items` | string[] | Yes | List of lesson document IDs |

**Relationships**:
- Belongs to one `SidebarConfiguration`
- References many `Lesson` entities via document IDs

**Validation Rules**:
- Type must be "category"
- Label must not be empty
- Items must be valid document IDs (relative paths without .md extension)
- Items must reference existing lesson files

**Example**:
```typescript
{
  type: 'category',
  label: 'Chapter 2: ROS 2 Fundamentals',
  items: [
    'chapter-02-ros2/lesson-01-architecture-core-concepts',
    'chapter-02-ros2/lesson-02-nodes-topics-services',
  ],
}
```

---

### 7. LessonTemplate

**Description**: Standardized template structure for lesson placeholders

**Attributes**:
| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `sections` | TemplateSection[] | Yes | Ordered list of template sections |
| `frontmatter_fields` | string[] | Yes | Required YAML frontmatter fields |

**Relationships**:
- Referenced by many `Lesson` entities

**Persistence**:
- **Definition**: Embedded in agent specification
- **Output**: Applied when generating lesson .md files

**Validation Rules**:
- Must contain all 7 mandatory sections (per FR-007)
- Section order must be preserved
- Frontmatter must include: sidebar_position, title, description

**Template Sections** (from FR-007):
1. Introduction
2. Learning Objectives
3. Key Concepts
4. Hands-on Exercise
5. Quiz
6. Key Takeaways
7. Further Reading

**Template Output Example**:
```markdown
---
sidebar_position: 1
title: Introduction to Embodied Intelligence
description: Foundations of Physical AI and embodied intelligence principles
---

# Introduction to Embodied Intelligence

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

## Hands-on Exercise
[Practical exercise or tutorial]

## Quiz
1. [Question 1]
2. [Question 2]

## Key Takeaways
- [Takeaway 1]
- [Takeaway 2]

## Further Reading
- [Resource 1]
- [Resource 2]
```

---

## Entity Relationships Diagram

```text
CourseContent (COURSE_CONTENT.md)
  │
  ├── 1:N → Module (Chapter)
  │           │
  │           ├── Persisted as: docs/docs/chapter-##-slug/
  │           │
  │           ├── 1:1 → CategoryConfiguration
  │           │           │
  │           │           └── Persisted as: _category_.json
  │           │
  │           └── 1:N → Lesson
  │                       │
  │                       ├── Persisted as: lesson-##-slug.md
  │                       │
  │                       └── References → LessonTemplate
  │
  └── 1:1 → SidebarConfiguration (sidebars.ts)
                │
                └── 1:N → SidebarCategory
                            │
                            └── References N → Lesson (via document IDs)
```

---

## State Transitions

### Agent Execution Flow

```text
1. PARSE_INPUT
   ├── Read COURSE_CONTENT.md → CourseContent entity
   ├── Parse chapter selection parameter
   └── Extract Module and Lesson entities

2. VALIDATE_INPUT
   ├── Check COURSE_CONTENT.md exists
   ├── Validate chapter/lesson selection format
   ├── Check docs/docs/ directory exists
   └── Verify write permissions

3. GENERATE_CHAPTERS
   For each selected Module:
   ├── Create Chapter directory
   ├── Generate CategoryConfiguration
   ├── Write _category_.json
   └── Log operation

4. GENERATE_LESSONS
   For each selected Lesson:
   ├── Apply LessonTemplate
   ├── Populate frontmatter
   ├── Write lesson .md file
   └── Log operation

5. UPDATE_SIDEBAR
   ├── Read existing sidebars.ts
   ├── Parse SidebarConfiguration
   ├── Append new SidebarCategory entries
   ├── Preserve manual edits
   ├── Write updated sidebars.ts
   └── Validate TypeScript syntax

6. VALIDATE_OUTPUT
   ├── Check all expected files exist
   ├── Validate _category_.json JSON syntax
   ├── Validate sidebars.ts TypeScript syntax
   ├── Run npm run build in docs/
   └── Report success or errors
```

---

## Persistence Strategy

| Entity | Storage Location | Format | Mutability |
|--------|------------------|--------|------------|
| CourseContent | COURSE_CONTENT.md | Markdown | Read-only |
| Module | docs/docs/chapter-##-slug/ | Directory | Agent creates |
| CategoryConfiguration | _category_.json | JSON | Agent creates |
| Lesson | lesson-##-slug.md | Markdown | Agent creates |
| SidebarConfiguration | docs/sidebars.ts | TypeScript | Agent updates |
| LessonTemplate | Agent specification | Embedded | Static |

---

## Validation Rules Summary

### File-Level Validation
- All JSON files must be valid JSON
- All TypeScript files must be valid TypeScript
- All Markdown files must have valid YAML frontmatter

### Business Logic Validation
- Chapter numbers must be 1-6
- Lesson numbers must be 1-4
- Slugs must be unique within their scope
- Document IDs in sidebar must reference existing files

### Referential Integrity
- Every SidebarCategory must reference existing Lessons
- Every Lesson must belong to an existing Module
- Every CategoryConfiguration must map to an existing Module directory

---

**Data model complete. Ready to define contracts.**
