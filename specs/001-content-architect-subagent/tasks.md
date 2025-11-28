# Tasks: Content Architect Subagent

**Branch**: `001-content-architect-subagent` | **Date**: 2025-11-28
**Spec**: [spec.md](./spec.md) | **Plan**: [plan.md](./plan.md)

---

## Task Summary

**Total Tasks**: 18
**Parallelizable Tasks**: 8
**User Stories**: 3 (P1: Initial Structure, P2: Lesson Quality, P3: Future Expansion)
**Suggested MVP**: User Story 1 only (P1) - 10 tasks

---

## Implementation Strategy

### Approach: User Story-Driven Incremental Delivery

1. **Phase 1 (Setup)**: Initialize agent file structure
2. **Phase 2 (Foundational)**: Core parsing and slug generation utilities
3. **Phase 3 (User Story 1 - P1)**: MVP - Basic scaffolding with directory/file creation
4. **Phase 4 (User Story 2 - P2)**: Enhanced lesson templates with 7 sections
5. **Phase 5 (User Story 3 - P3)**: Idempotent re-run and expansion support
6. **Phase 6 (Polish)**: Validation, error handling, performance optimization

### Independent Testing Per Story

- **US1**: Invoke agent → verify 6 lesson files + 3 chapters + sidebar updated + Docusaurus build succeeds
- **US2**: Inspect generated lesson files → verify 7 mandatory sections + frontmatter
- **US3**: Run agent twice → verify no duplicates + new lessons added without overwriting existing

### MVP Scope (Minimum Viable Product)

**Deliver US1 Only** = Core scaffolding functionality:
- Reads COURSE_CONTENT.md
- Creates chapter directories
- Generates basic lesson files
- Updates sidebars.ts
- Validates output

**Deferred to Post-MVP**:
- US2 (enhanced templates) - adds polish
- US3 (idempotency) - adds robustness

---

## Dependencies & Execution Order

### Story Completion Order

```
Setup → Foundational → US1 (P1) → US2 (P2) → US3 (P3) → Polish
```

### Task Dependencies

**Blocking Tasks** (must complete before user stories):
- T001: Agent file creation (blocks all)
- T002: COURSE_CONTENT parser (blocks US1, US2, US3)
- T003: Slug generator (blocks US1, US2, US3)

**User Story Dependencies**:
- US2 depends on US1 (enhances templates created by US1)
- US3 depends on US1 (adds idempotency to US1 functionality)

**Independent Stories**: None (sequential dependencies)

---

## Parallel Execution Opportunities

### Within User Story 1 (P1)
```bash
# After T003 completes, run in parallel:
- T004 [P] [US1] Chapter directory generator
- T005 [P] [US1] Lesson file generator
- T006 [P] [US1] Category config generator

# After T004-T006 complete, run sequentially:
- T007 [US1] Sidebar updater
- T008 [US1] Main orchestrator
- T009 [US1] Validation suite
- T010 [US1] Integration test
```

### Within User Story 2 (P2)
```bash
# All template section writers can run in parallel after US1:
- T011 [P] [US2] Introduction template
- T012 [P] [US2] Learning Objectives template
- T013 [P] [US2] Key Concepts template
- T014 [P] [US2] Hands-on Exercise template
```

### Within User Story 3 (P3)
```bash
# All idempotency handlers can run in parallel:
- T015 [P] [US3] File existence checker
- T016 [P] [US3] Duplicate detection
```

---

# Phase 1: Setup

**Goal**: Initialize agent file structure and dependencies

**Tasks**:

- [X] T001 Create agent specification file at .claude/agents/content-architect.md with YAML frontmatter (name, description, version, tools)

**Acceptance**: File exists, valid YAML frontmatter, tools section lists Read/Write/Glob/Bash

---

# Phase 2: Foundational (Blocking Prerequisites)

**Goal**: Core utilities needed by all user stories

**Tasks**:

- [X] T002 Implement COURSE_CONTENT.md parser in agent that extracts Module headers and lesson topics using regex pattern matching
- [X] T003 Implement slug generation function that converts titles to kebab-case (lowercase, hyphens, alphanumeric only)

**Acceptance**:
- T002: Parser extracts "Module 1: Title" → {number: 1, title: "Title"}
- T003: Slug generator converts "ROS 2 Architecture & Core Concepts" → "ros-2-architecture-core-concepts"

---

# Phase 3: User Story 1 (P1) - Initial Structure Generation

**Story Goal**: Generate complete directory structure for 3 chapters with 2 lessons each

**Independent Test**: Invoke agent with "chapters 1-3, lessons 1-2" → Verify all files created → Run `npm run build` in docs/ → Build succeeds

**Acceptance Criteria** (from spec.md):
1. Three chapter directories created (chapter-01-foundations/, chapter-02-ros2/, chapter-03-simulation/)
2. Exactly 2 lesson markdown files per chapter (lesson-01-*.md, lesson-02-*.md)
3. Each chapter has valid _category_.json (label, position, description)
4. docs/sidebars.ts updated with 3 chapters and 6 lessons
5. Docusaurus build succeeds without errors

**Tasks**:

- [X] T004 [P] [US1] Implement chapter directory generator that creates docs/docs/chapter-##-{slug}/ directories based on parsed course content
- [X] T005 [P] [US1] Implement lesson file generator that creates lesson-##-{slug}.md files with basic frontmatter (sidebar_position, title, description)
- [X] T006 [P] [US1] Implement _category_.json generator that creates category config files with label, position, collapsed=false, and generated-index link
- [X] T007 [US1] Implement sidebar updater that reads docs/sidebars.ts, parses tutorialSidebar array, appends new category objects, and writes back with valid TypeScript syntax
- [X] T008 [US1] Implement main agent orchestrator that chains: parse input → generate chapters → generate lessons → generate configs → update sidebar → validate
- [X] T009 [US1] Implement validation suite that checks file existence (6 lessons, 3 _category_.json), JSON syntax validity, and TypeScript syntax validity
- [X] T010 [US1] Create integration test script that invokes agent, verifies all outputs, and runs Docusaurus build test

**US1 Completion Test**:
```bash
# From repository root
claude agent content-architect << EOF
chapters 1-3, lessons 1-2
EOF

# Verify outputs
ls -la docs/docs/chapter-01-foundations/
ls -la docs/docs/chapter-02-ros2/
ls -la docs/docs/chapter-03-simulation/

# Validate
cd docs && npm run build  # Should exit 0
```

---

# Phase 4: User Story 2 (P2) - Lesson Placeholder Quality

**Story Goal**: Enhance lesson templates with 7 mandatory sections and helpful placeholder text

**Independent Test**: Generate lessons → Inspect lesson files → Verify all 7 sections present with placeholder text

**Acceptance Criteria** (from spec.md):
1. Frontmatter contains sidebar_position, title, description
2. All 7 mandatory sections present: Introduction, Learning Objectives, Key Concepts, Hands-on Exercise, Quiz, Key Takeaways, Further Reading
3. Each section contains helpful placeholder text guiding content writers

**Tasks**:

- [X] T011 [P] [US2] Extend lesson file generator to include Introduction section with placeholder text guiding overview content
- [X] T012 [P] [US2] Add Learning Objectives section with 3 bulleted placeholders using action verbs (define, explain, apply)
- [X] T013 [P] [US2] Add Key Concepts section with 3 subsections (###) each containing explanation placeholders
- [X] T014 [P] [US2] Add Hands-on Exercise section with Prerequisites, Steps (1-3), and Expected Outcome placeholders
- [X] T015 [P] [US2] Add Quiz section with 3 multiple-choice questions (A-D options) and hidden answers in `<details>` tag
- [X] T016 [P] [US2] Add Key Takeaways section with 3 bulleted summary placeholders
- [X] T017 [P] [US2] Add Further Reading section with 3 resource link placeholders and "Next Lesson" navigation link

**US2 Completion Test**:
```bash
# Generate lessons with enhanced templates
claude agent content-architect << EOF
chapters 1-3, lessons 1-2
EOF

# Inspect generated lesson
cat docs/docs/chapter-01-foundations/lesson-01-*.md

# Verify sections (should output 7)
grep -c "^## " docs/docs/chapter-01-foundations/lesson-01-*.md
```

---

# Phase 5: User Story 3 (P3) - Future Expansion Support

**Story Goal**: Enable idempotent re-runs and incremental expansion without breaking existing structure

**Independent Test**: Run agent twice with same params → No duplicates | Run agent with expanded params → New lessons added, existing preserved

**Acceptance Criteria** (from spec.md):
1. Running agent with lesson 3 added creates lesson-03-*.md in each chapter without overwriting lesson-01 and lesson-02
2. Running agent with chapters 4-6 creates new chapter directories and updates sidebar without duplicating chapters 1-3
3. Docusaurus build succeeds after expansion

**Tasks**:

- [X] T018 [P] [US3] Implement file existence checker that detects existing chapter directories and lesson files before creation
- [X] T019 [P] [US3] Implement user confirmation prompt for file overwrites (ask "Overwrite? (y/n)" if file exists)
- [X] T020 [US3] Implement duplicate detection in sidebar updater that checks for existing category labels before appending
- [X] T021 [US3] Extend chapter generator to skip creation if directory exists, log warning instead
- [X] T022 [US3] Extend lesson generator to skip creation if file exists unless user confirms overwrite
- [X] T023 [US3] Add integration test for idempotent re-run (run agent twice, verify no duplicates)
- [X] T024 [US3] Add integration test for incremental expansion (run agent with 2 lessons, then 3 lessons, verify 3rd added)

**US3 Completion Test**:
```bash
# First run: chapters 1-3, lessons 1-2
claude agent content-architect << EOF
chapters 1-3, lessons 1-2
EOF

# Second run: same params (should detect existing, skip duplicates)
claude agent content-architect << EOF
chapters 1-3, lessons 1-2
EOF

# Third run: expanded params (should add lesson-03 to each chapter)
claude agent content-architect << EOF
chapters 1-3, lessons 1-3
EOF

# Verify 3 lessons per chapter
ls docs/docs/chapter-01-foundations/ | grep lesson | wc -l  # Should output 3
```

---

# Phase 6: Polish & Cross-Cutting Concerns

**Goal**: Error handling, performance optimization, logging, documentation

**Tasks**:

- [X] T025 Implement error handling for COURSE_CONTENT.md missing (fail with clear message per edge case in spec)
- [X] T026 Implement error handling for docs/docs/ directory not found (fail with clear message)
- [X] T027 Implement error handling for permission denied errors (fail with clear message and fix suggestion)
- [X] T028 Add operation logging that outputs file creation operations to console (FR-009)
- [X] T029 Add execution summary that displays created directories, created files, updated files, validation status, and execution time
- [X] T030 Optimize performance to meet <30s execution time target (SC-001) - profile and optimize bottlenecks
- [X] T031 Add agent usage documentation in .claude/agents/content-architect.md header with examples
- [X] T032 Create validation test suite that runs all acceptance scenarios from spec.md

**Acceptance**:
- T025-T027: Each error scenario displays clear error message and fails gracefully
- T028: Console shows log entry for each file created
- T029: Execution summary matches format in contracts/agent-interface.md
- T030: Profiling shows execution completes in <30s for 6 lessons
- T031: Agent file header contains clear usage instructions
- T032: All 13 acceptance scenarios from spec.md pass

---

## Task Completion Checklist

### Setup (1 task)
- [X] T001 - Agent file created

### Foundational (2 tasks)
- [X] T002 - Parser implemented
- [X] T003 - Slug generator implemented

### User Story 1 - P1 (7 tasks)
- [X] T004 - Chapter generator
- [X] T005 - Lesson generator
- [X] T006 - Category config generator
- [X] T007 - Sidebar updater
- [X] T008 - Main orchestrator
- [X] T009 - Validation suite
- [X] T010 - Integration test

### User Story 2 - P2 (7 tasks)
- [X] T011 - Introduction section
- [X] T012 - Learning Objectives section
- [X] T013 - Key Concepts section
- [X] T014 - Hands-on Exercise section
- [X] T015 - Quiz section
- [X] T016 - Key Takeaways section
- [X] T017 - Further Reading section

### User Story 3 - P3 (7 tasks)
- [X] T018 - File existence checker
- [X] T019 - User confirmation prompt
- [X] T020 - Duplicate detection
- [X] T021 - Skip existing directories
- [X] T022 - Skip existing lessons
- [X] T023 - Idempotent test
- [X] T024 - Incremental expansion test

### Polish (8 tasks)
- [X] T025 - Error: COURSE_CONTENT.md missing
- [X] T026 - Error: docs/docs/ not found
- [X] T027 - Error: Permission denied
- [X] T028 - Operation logging
- [X] T029 - Execution summary
- [X] T030 - Performance optimization
- [X] T031 - Usage documentation
- [X] T032 - Validation test suite

---

## Format Validation

✅ **All tasks follow checklist format**: `- [ ] [TaskID] [P?] [Story?] Description with file path`
✅ **Task IDs**: Sequential (T001-T032)
✅ **[P] markers**: 8 parallelizable tasks identified
✅ **[Story] labels**: All user story tasks labeled (US1, US2, US3)
✅ **File paths**: All implementation tasks reference specific file (.claude/agents/content-architect.md)
✅ **Independent testing**: Each user story has clear acceptance test

---

## Next Steps

1. **Run `/sp.implement`** to execute tasks in order
2. **MVP Delivery**: Complete Phases 1-3 (T001-T010) for initial scaffolding capability
3. **Incremental Enhancement**: Add Phases 4-5 (T011-T024) for template quality and expansion
4. **Production Ready**: Complete Phase 6 (T025-T032) for robustness and polish

---

**Tasks generated successfully. Ready for implementation.**
