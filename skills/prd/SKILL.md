---
name: prd
description: "Generate a Product Requirements Document (PRD) for a project. Use when starting a new project, defining product vision, or when asked to create a PRD. Triggers on: create a prd, write prd for, plan this project, product requirements, spec out."
---

# PRD Generator

Create the **canonical Product Requirements Document** for a project. This is the single source of truth for product vision, context, goals, and constraints.

---

## The Job

1. Receive a project/product description from the user
2. Ask 3-5 essential clarifying questions (with lettered options)
3. Generate a structured PRD based on answers
4. Save to `prd.md` (project root)

**Important:** Do NOT create implementation plans or user stories. Just create the high-level PRD.

---

## Relationship to Implementation Plans

The PRD (`prd.md`) is the **project-level** document. It defines:
- Product vision and purpose
- Target users and their problems
- High-level features and scope
- Constraints and non-goals

**Implementation Plans** (created by the `ip` skill) are **feature-level** documents that:
- Reference `prd.md` for context
- Break down specific features into user stories
- Are converted to `ralph.json` for autonomous execution

**Flow:** `prd.md` (one per project) → multiple `implementation-plan-*.md` → one `ralph.json` at a time

---

## Step 1: Clarifying Questions

Ask only critical questions where the initial prompt is ambiguous. Focus on:

- **Problem/Goal:** What problem does this solve? Why does this project exist?
- **Target Users:** Who is this for?
- **Core Value:** What's the main thing it does?
- **Scope/Boundaries:** What should it NOT do?

### Format Questions Like This:

```
1. What is the primary problem this solves?
   A. [Option based on context]
   B. [Option based on context]
   C. [Option based on context]
   D. Other: [please specify]

2. Who is the target user?
   A. Developers
   B. End consumers
   C. Internal teams
   D. Other: [please specify]

3. What is the initial scope?
   A. Proof of concept / MVP
   B. Production-ready v1
   C. Enhancement to existing product
```

This lets users respond with "1A, 2C, 3B" for quick iteration.

---

## Step 2: PRD Structure

Generate the PRD with these sections:

### 1. Overview
What is this product? A 2-3 sentence elevator pitch.

### 2. Problem Statement
What problem does this solve? Why does it need to exist?

### 3. Target Users
Who is this for? Describe the primary user personas.

### 4. Goals
What are the high-level objectives? (bullet list)

### 5. Key Features
List the major features/capabilities at a high level. No implementation details—just what the product does.

Example:
- User authentication (login, signup, password reset)
- Dashboard with key metrics
- Data export functionality

### 6. Non-Goals (Out of Scope)
What this product will NOT include. Critical for managing scope.

### 7. Constraints
Known limitations, technical constraints, dependencies, or requirements that shape the solution.

### 8. Success Criteria
How will we know the product is successful?

### 9. Open Questions
Remaining questions or areas needing clarification.

---

## Writing Style

- Be clear and concise
- Focus on the "what" and "why", not the "how"
- Avoid implementation details—those belong in implementation plans
- Use concrete language, avoid jargon
- Write for someone who knows nothing about the project

---

## Output

- **Format:** Markdown (`.md`)
- **Location:** Project root
- **Filename:** `prd.md`

---

## Example PRD

```markdown
# PRD: TaskFlow

## Overview

TaskFlow is a lightweight task management app for individuals who want a simple, fast way to track their daily to-dos without the complexity of enterprise project management tools.

## Problem Statement

Existing task management tools are either too simple (notes apps) or too complex (Jira, Asana). Users who want basic task tracking with just enough structure—like priorities and due dates—don't have a good middle-ground option.

## Target Users

- Individual professionals managing personal workloads
- Freelancers tracking client tasks
- Students organizing assignments and deadlines

## Goals

- Provide a fast, distraction-free task management experience
- Support basic organization: priorities, due dates, projects
- Work offline-first with cloud sync
- Launch MVP within 4 weeks

## Key Features

- Task creation with title, description, priority, and due date
- Project/folder organization
- Priority levels (high, medium, low) with visual indicators
- Due date reminders
- Filter and search tasks
- Offline support with sync

## Non-Goals

- Team collaboration features
- Time tracking
- Integrations with other tools (v1)
- Mobile apps (web-first, responsive design only)

## Constraints

- Must work in modern browsers (Chrome, Firefox, Safari, Edge)
- Data stored locally with optional cloud backup
- Single-developer project, keep complexity low

## Success Criteria

- User can create and complete a task in under 10 seconds
- App loads in under 2 seconds
- Works fully offline after initial load
- Positive feedback from 5 beta testers

## Open Questions

- Should we support recurring tasks in v1?
- What cloud sync provider to use?
- Monetization strategy (free, freemium, paid)?
```

---

## Checklist

Before saving the PRD:

- [ ] Asked clarifying questions with lettered options
- [ ] Incorporated user's answers
- [ ] Focused on vision/goals, NOT implementation details
- [ ] Non-goals section defines clear boundaries
- [ ] Saved to `prd.md`
