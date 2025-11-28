# Project Requirements (Updated)

This document outlines the finalized requirements and scope for Phase 1 of the project, including subagents, skills, textbook structure, and RAG chatbot expectations.

---

## 📚 Phase 1 Textbook Scope (Before Sunday)
- Only **3 lessons** will contain real written content:
  - **1.1** — Introduction to Embodied Intelligence
  - **2.1** — ROS 2 Architecture and Core Concepts
  - **3.1** — Gazebo Simulation Environment

- The **sidebar will show 2 lessons per chapter** for Chapters 1, 2, and 3:
  - Chapter 1 → 1.1, 1.2
  - Chapter 2 → 2.1, 2.2
  - Chapter 3 → 3.1, 3.2

- Placeholder lessons **1.2, 2.2, 3.2** will display the **Lesson Template Generator's output** instead of actual content.
  - This keeps the structure complete without requiring full text.

- Chapters **4, 5, and 6** remain post-hackathon.

---

## 🤖 Subagent Selection
### Active in Phase 1
- **Content Architect Subagent**
- **Technical Writer Subagent**

### Post-Hackathon
- **Assessment Creator Subagent**

---

## 🛠 Skills Selection
### Active Skills (Phase 1)
- Chapter Scaffolder
- Lesson Template Generator
  - Also used to populate placeholder lessons
- Sidebar Config Updater
- Cross-Reference Linker

### Post-Hackathon Skills
- Diagram Placeholder Creator
- Interactive Quiz Generator
- Urdu Translator Skill

---

## 🔒 Optional Future Feature
- **BetterAuth-based Authentication**
  - Signup / Login functionality
  - Requires database + user journey
  - Not included in Phase 1

---

## 🔴 Mandatory RAG Requirement
The RAG Chatbot **must be completed before submission**.

### RAG Stack:
- Qdrant vector store
- OpenAI embeddings
- FastAPI backend
- ChatKit UI
- Highlight → Ask flow (text-selection assistance)

### Deadline:
**Sunday, 30 Nov — 6 PM (Karachi/Islamabad)**

---

## 📝 Phase 1 Deliverables Summary
- 3 fully written lessons
- Sidebar displays 6 lessons (3 placeholders via Lesson Template Generator)
- Content Architect + Technical Writer active
- 4 core skills active
- RAG Chatbot implemented
- All remaining content and features done after the hackathon

---

This README serves as the canonical reference for all updated project requirements for Phase 1.