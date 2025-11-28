# Lesson Template Contract

**Version**: 1.0.0
**Date**: 2025-11-28
**Purpose**: Define the standardized structure for lesson placeholder markdown files

---

## Template Structure

### Complete Template

```markdown
---
sidebar_position: {{SIDEBAR_POSITION}}
title: {{LESSON_TITLE}}
description: {{LESSON_DESCRIPTION}}
---

# {{LESSON_TITLE}}

## Introduction

{{INTRODUCTION_PLACEHOLDER}}

## Learning Objectives

By the end of this lesson, you will be able to:

- {{OBJECTIVE_1}}
- {{OBJECTIVE_2}}
- {{OBJECTIVE_3}}

## Key Concepts

### {{CONCEPT_1_TITLE}}

{{CONCEPT_1_EXPLANATION}}

### {{CONCEPT_2_TITLE}}

{{CONCEPT_2_EXPLANATION}}

### {{CONCEPT_3_TITLE}}

{{CONCEPT_3_EXPLANATION}}

## Hands-on Exercise

{{EXERCISE_DESCRIPTION}}

**Prerequisites:**
- {{PREREQUISITE_1}}
- {{PREREQUISITE_2}}

**Steps:**
1. {{STEP_1}}
2. {{STEP_2}}
3. {{STEP_3}}

**Expected Outcome:**
{{EXPECTED_OUTCOME}}

## Quiz

Test your understanding of this lesson:

1. {{QUESTION_1}}
   - A) {{OPTION_A}}
   - B) {{OPTION_B}}
   - C) {{OPTION_C}}
   - D) {{OPTION_D}}

2. {{QUESTION_2}}
   - A) {{OPTION_A}}
   - B) {{OPTION_B}}
   - C) {{OPTION_C}}
   - D) {{OPTION_D}}

3. {{QUESTION_3}}
   - A) {{OPTION_A}}
   - B) {{OPTION_B}}
   - C) {{OPTION_C}}
   - D) {{OPTION_D}}

<details>
<summary>Show Answers</summary>

1. {{ANSWER_1}} - {{EXPLANATION_1}}
2. {{ANSWER_2}} - {{EXPLANATION_2}}
3. {{ANSWER_3}} - {{EXPLANATION_3}}

</details>

## Key Takeaways

- {{TAKEAWAY_1}}
- {{TAKEAWAY_2}}
- {{TAKEAWAY_3}}

## Further Reading

- [{{RESOURCE_1_TITLE}}]({{RESOURCE_1_URL}})
- [{{RESOURCE_2_TITLE}}]({{RESOURCE_2_URL}})
- [{{RESOURCE_3_TITLE}}]({{RESOURCE_3_URL}})

---

**Next Lesson**: [{{NEXT_LESSON_TITLE}}]({{NEXT_LESSON_LINK}})
```

---

## Placeholder Variables

### Frontmatter Variables

| Variable | Type | Source | Example |
|----------|------|--------|---------|
| `{{SIDEBAR_POSITION}}` | integer | Lesson.sidebar_position | `1` |
| `{{LESSON_TITLE}}` | string | Lesson.title | `"Introduction to Embodied Intelligence"` |
| `{{LESSON_DESCRIPTION}}` | string | Lesson.description | `"Foundations of Physical AI and embodied intelligence principles"` |

### Content Variables

| Variable | Type | Fill Strategy | Example |
|----------|------|---------------|---------|
| `{{INTRODUCTION_PLACEHOLDER}}` | string | Generic prompt | `"[Provide an overview of embodied intelligence and its relevance to Physical AI]"` |
| `{{OBJECTIVE_N}}` | string | Generic prompt | `"[Define what students will learn]"` |
| `{{CONCEPT_N_TITLE}}` | string | Topic extraction | `"What is Embodied Intelligence?"` |
| `{{CONCEPT_N_EXPLANATION}}` | string | Generic prompt | `"[Explain the concept in detail]"` |
| `{{EXERCISE_DESCRIPTION}}` | string | Generic prompt | `"[Describe a hands-on exercise related to this topic]"` |
| `{{QUESTION_N}}` | string | Generic prompt | `"[Multiple choice question]"` |
| `{{TAKEAWAY_N}}` | string | Generic prompt | `"[Key point students should remember]"` |
| `{{RESOURCE_N_TITLE}}` | string | Generic prompt | `"[External resource title]"` |
| `{{RESOURCE_N_URL}}` | string | Placeholder | `"#"` |
| `{{NEXT_LESSON_TITLE}}` | string | Next lesson in sequence | `"Robotics Landscape"` |
| `{{NEXT_LESSON_LINK}}` | string | Next lesson filename | `"./lesson-02-robotics-landscape"` |

---

## Mandatory Sections (FR-007)

The following 7 sections are **required** in every lesson placeholder:

1. ✅ **Introduction** - Overview of the topic
2. ✅ **Learning Objectives** - Clear, measurable objectives
3. ✅ **Key Concepts** - Main concepts with explanations
4. ✅ **Hands-on Exercise** - Practical exercise or tutorial
5. ✅ **Quiz** - Assessment questions
6. ✅ **Key Takeaways** - Summary points
7. ✅ **Further Reading** - External resources

**Validation**: Agent must verify all 7 sections present before writing file.

---

## Section Specifications

### 1. Introduction

**Purpose**: Provide context and motivate the topic

**Format**:
```markdown
## Introduction

[Overview of the topic and its relevance to Physical AI]
[Why this topic matters]
[What students will explore]
```

**Length**: 1-2 paragraphs

**Placeholder Text**:
```markdown
[Provide an overview of {{LESSON_TITLE}} and explain how it relates to Physical AI and Humanoid Robotics. Include why this topic is important for students to master.]
```

---

### 2. Learning Objectives

**Purpose**: Set clear expectations for learning outcomes

**Format**:
```markdown
## Learning Objectives

By the end of this lesson, you will be able to:

- [Objective 1 - using action verb]
- [Objective 2 - using action verb]
- [Objective 3 - using action verb]
```

**Requirements**:
- Minimum 3 objectives
- Use Bloom's Taxonomy action verbs (understand, apply, analyze, etc.)
- Be specific and measurable

**Placeholder Text**:
```markdown
- [Define key terms related to {{LESSON_TITLE}}]
- [Explain the core principles and concepts]
- [Apply the concepts in practical scenarios]
```

---

### 3. Key Concepts

**Purpose**: Explain main concepts in depth

**Format**:
```markdown
## Key Concepts

### Concept 1 Title

[Detailed explanation of concept 1]

### Concept 2 Title

[Detailed explanation of concept 2]

### Concept 3 Title

[Detailed explanation of concept 3]
```

**Requirements**:
- Minimum 3 concepts
- Each concept is a level-3 heading (###)
- Include explanations, examples, and diagrams (placeholders)

**Placeholder Text**:
```markdown
### [Concept Name]

[Provide a clear explanation of this concept. Include:
- Definition
- How it works
- Why it's important
- Real-world examples]

[Optional: Add diagram placeholder]
*[Diagram: Visual representation of {{CONCEPT_NAME}}]*
```

---

### 4. Hands-on Exercise

**Purpose**: Provide practical application of concepts

**Format**:
```markdown
## Hands-on Exercise

[Exercise description and goals]

**Prerequisites:**
- [Required knowledge/tools]
- [Required software/hardware]

**Steps:**
1. [Step 1 with clear instructions]
2. [Step 2 with clear instructions]
3. [Step 3 with clear instructions]

**Expected Outcome:**
[What students should achieve by completing this exercise]
```

**Requirements**:
- Clear prerequisites listed
- Step-by-step instructions
- Expected outcome defined
- Difficulty appropriate for lesson level

**Placeholder Text**:
```markdown
[Design a hands-on exercise that allows students to practice {{LESSON_TITLE}}. Include:
- Clear steps to follow
- Code snippets (if applicable)
- Expected results
- Common pitfalls to avoid]
```

---

### 5. Quiz

**Purpose**: Assess understanding of lesson material

**Format**:
```markdown
## Quiz

Test your understanding of this lesson:

1. [Question 1]
   - A) [Option A]
   - B) [Option B]
   - C) [Option C]
   - D) [Option D]

2. [Question 2]
   - A) [Option A]
   - B) [Option B]
   - C) [Option C]
   - D) [Option D]

3. [Question 3]
   - A) [Option A]
   - B) [Option B]
   - C) [Option C]
   - D) [Option D]

<details>
<summary>Show Answers</summary>

1. [Correct answer letter] - [Explanation]
2. [Correct answer letter] - [Explanation]
3. [Correct answer letter] - [Explanation]

</details>
```

**Requirements**:
- Minimum 3 multiple-choice questions
- 4 options per question (A, B, C, D)
- Answers hidden in collapsible section
- Include explanations for correct answers

**Placeholder Text**:
```markdown
[Create multiple-choice questions that test understanding of:
- Key definitions
- Concept applications
- Problem-solving scenarios
Ensure questions align with Learning Objectives]
```

---

### 6. Key Takeaways

**Purpose**: Summarize essential points

**Format**:
```markdown
## Key Takeaways

- [Takeaway 1 - most important point]
- [Takeaway 2 - second most important point]
- [Takeaway 3 - third most important point]
```

**Requirements**:
- Minimum 3 takeaways
- Concise bullet points (1-2 sentences each)
- Focus on actionable insights

**Placeholder Text**:
```markdown
- [Summarize the most critical point students must remember from {{LESSON_TITLE}}]
- [Highlight a key skill or concept they should now be able to apply]
- [Note any important warnings or best practices]
```

---

### 7. Further Reading

**Purpose**: Provide resources for deeper learning

**Format**:
```markdown
## Further Reading

- [Resource 1 Title](URL) - Brief description
- [Resource 2 Title](URL) - Brief description
- [Resource 3 Title](URL) - Brief description
```

**Requirements**:
- Minimum 3 external resources
- Include official documentation, tutorials, research papers
- Prefer authoritative sources

**Placeholder Text**:
```markdown
- [Official Documentation: {{TOPIC_NAME}}](#) - [Description of what this resource covers]
- [Tutorial: {{TOPIC_NAME}} Hands-on Guide](#) - [Description]
- [Research Paper: {{TOPIC_NAME}} Advances](#) - [Description]
```

---

## Navigation Section

### Next Lesson Link

**Purpose**: Guide students to the next lesson in sequence

**Format**:
```markdown
---

**Next Lesson**: [Lesson Title](./lesson-##-slug)
```

**Logic**:
- If current lesson is NOT the last in chapter:
  - Link to next lesson in same chapter
  - Example: lesson-01 links to lesson-02

- If current lesson IS the last in chapter:
  - Link to first lesson of next chapter (if exists)
  - Example: chapter-01/lesson-02 links to chapter-02/lesson-01

- If current lesson is the last lesson of last chapter:
  - Omit Next Lesson section or link to conclusion page

**Placeholder Text**:
```markdown
**Next Lesson**: [{{NEXT_LESSON_TITLE}}]({{NEXT_LESSON_LINK}})
```

---

## Frontmatter Schema

### Required Fields

```yaml
---
sidebar_position: 1           # integer, 1-based position within chapter
title: "Lesson Title"         # string, display name in sidebar
description: "Brief summary"  # string, SEO and metadata
---
```

### Optional Fields (Future Enhancement)

```yaml
---
sidebar_position: 1
title: "Lesson Title"
description: "Brief summary"
tags: ["ros2", "robotics"]    # array of strings, for filtering
difficulty: "beginner"         # enum: beginner, intermediate, advanced
estimated_time: "45 minutes"  # string, time to complete
---
```

**Current Version**: Only required fields (sidebar_position, title, description)

---

## Template Population Strategy

### Phase 1 (Content Architect Subagent)

**Approach**: Fill with generic placeholder text

**Example**:
```markdown
## Introduction

[Provide an overview of Introduction to Embodied Intelligence and explain how it relates to Physical AI and Humanoid Robotics. Include why this topic is important for students to master.]
```

**Rationale**: Content Architect focuses on structure, not content. Placeholder text guides the Technical Writer Subagent.

### Phase 2 (Technical Writer Subagent)

**Approach**: Replace placeholders with actual content

**Example**:
```markdown
## Introduction

Embodied intelligence represents a paradigm shift in artificial intelligence, moving beyond purely computational systems to AI that exists in and interacts with the physical world. This lesson explores how Physical AI systems combine perception, reasoning, and action to understand and navigate physical environments, laying the foundation for humanoid robotics.
```

**Rationale**: Technical Writer Subagent uses COURSE_CONTENT.md and domain knowledge to generate high-quality educational content.

---

## Validation Rules

### Template Completeness

✅ All 7 mandatory sections present
✅ Frontmatter contains all required fields
✅ Markdown syntax is valid
✅ Links use relative paths (not absolute)
✅ Quiz answers are hidden in `<details>` tags

### Content Quality (Phase 2 Only)

- Introduction is 1-2 paragraphs
- Learning Objectives use action verbs
- Key Concepts have at least 3 subsections
- Hands-on Exercise has clear steps
- Quiz has at least 3 questions with explanations
- Key Takeaways are concise
- Further Reading has at least 3 resources

---

## Example: Populated Lesson

See [example-lesson.md](./example-lesson.md) for a fully populated lesson template.

---

**Template contract complete. This defines the structure every lesson file must follow.**
