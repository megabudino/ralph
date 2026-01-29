---
name: ip
description: "Generate an Implementation Plan (via user stories) for a feature, based on the project's canonical PRD. Use when: planning implementation, breaking down a feature, creating user stories/acceptance criteria, estimating scope. Triggers on: implementation plan, plan implementation, break down feature, user stories for, how to build, execution plan."
---

# IP — Implementation Plan Generator

Create detailed **Implementation Plans** that translate an existing PRD into an actionable, story-driven plan suitable for execution by a junior developer or AI agent.

---

## The Job

1. **Read `prd.md` (project-wide canonical PRD)** to understand product context, goals, constraints, and definitions.
2. Receive a feature request from the user (or a PRD section / feature name / link within `prd.md`).
3. Ask **3–5 essential clarifying questions** (with lettered options) *only if needed*.
4. Generate a structured **Implementation Plan** with user stories, acceptance criteria, and technical sequencing.
5. Save to `tasks/implementation-plan-[feature-name].md`

**Important:** Do **NOT** start implementing. Only produce the plan.

---

## Step 0: Read the Canonical PRD (`prd.md`)

Before asking questions or writing anything:

- Open and read `prd.md`.
- Extract and keep in mind:
  - Product goals and success metrics
  - Scope boundaries / non-goals
  - Target users and key workflows
  - Constraints (tech, legal, privacy, performance)
  - Any existing terminology and data model assumptions
  - Any referenced mockups, flows, or external docs

If `prd.md` is missing, outdated, or clearly unrelated:
- Ask the user to provide or update it, or point to the correct file.
- Do not proceed to planning until the canonical PRD is available.

---

## Step 1: Clarifying Questions (Only When Needed)

Ask only critical questions where:
- `prd.md` is ambiguous,
- the feature request conflicts with the PRD,
- or key implementation choices aren’t specified.

Focus on:

- **Scope Cut:** What is the minimal implementable slice?
- **UX Decisions:** What is the expected user interaction?
- **Data/State:** What must be persisted vs computed?
- **Integrations:** APIs, services, auth, permissions
- **Rollout:** flags, migration requirements, backward compatibility

### Format Questions Like This

Use lettered options so the user can reply quickly (e.g., `1B, 2A, 3D`):

```
1. Which release scope do you want for the first iteration?
   A. MVP (core happy path only)
   B. Standard (happy path + key edge cases)
   C. Full (includes all PRD edge cases + polish)
   D. Other: [specify]

2. What platforms are in scope?
   A. Web only
   B. Mobile only
   C. Web + Mobile
   D. Admin only

3. How should permissions work?
   A. Same as existing role model in PRD
   B. Admin-only initially
   C. Owner + Admin
   D. Other: [specify]
```

If `prd.md` already answers these, **skip questions** and move on.

---

## Step 2: Implementation Plan Structure

Generate the Implementation Plan with these sections.

### 1. Feature Summary
- Feature name
- 3–6 bullet recap derived from `prd.md`
- Link/anchor to the relevant PRD section (if applicable)

### 2. Goals and Success Criteria (From PRD)
- Bullet list (keep wording consistent with PRD)
- Add measurable “definition of done” when missing (without changing intent)

### 3. Assumptions
- List assumptions you’re making due to missing detail
- Each assumption should be testable/confirmable

### 4. Out of Scope (Non-Goals)
- Copy/align with PRD non-goals and add any implementation-specific exclusions

### 5. User Stories (Implementation Units)

Each story must be small enough to implement in one focused session.

Each story includes:
- **ID** (IP-001, IP-002, …)
- **Title**
- **Description**: `As a [user], I want [feature] so that [benefit].`
- **Acceptance Criteria**: verifiable checklist

**Format:**
```markdown
### IP-001: [Title]
**Description:** As a [user], I want [feature] so that [benefit].

**Acceptance Criteria:**
- [ ] Specific verifiable criterion
- [ ] Another verifiable criterion
- [ ] Typecheck/lint passes
- [ ] Tests added/updated (unit/integration) where applicable
- [ ] **[UI stories only]** Verify in browser using dev-browser skill
```

**Rules for Acceptance Criteria**
- Must be observable/verifiable, not vague.
- Prefer concrete UI states, API responses, validations, error states.
- Include edge cases when they are important to correctness.
- **Any UI change must include**: “Verify in browser using dev-browser skill”.

### 6. Functional Requirements Mapping
Create a numbered list that maps plan items to PRD requirements.

Example:
- `FR-1 (PRD §3.2): ...`
- `FR-2 (PRD §4.1): ...`

If PRD requirements aren’t numbered, create stable identifiers and reference the PRD section headings.

### 7. Technical Plan and Sequencing
Provide an ordered implementation sequence such as:

1. Data model / migrations
2. API layer / services
3. UI components and flows
4. State management / caching
5. Permissions and security
6. Observability / logging
7. Cleanup / refactors

For each step:
- Mention impacted modules/areas
- Call out risky parts and mitigations

### 8. Data & Migration Notes (If Applicable)
- Schema changes
- Backfills
- Compatibility considerations
- Rollback strategy (even if “not supported”, state it)

### 9. Testing Plan
- Unit tests: what functions/logic
- Integration tests: what endpoints/flows
- E2E/UI checks: what to verify manually + dev-browser usage
- Regression areas to watch

### 10. Rollout Plan
- Feature flags (if needed)
- Gradual rollout vs big-bang
- Monitoring signals
- Fallback plan

### 11. Risks and Open Questions
- Known risks (performance, data integrity, UX confusion, permissions)
- Remaining open questions not answered by PRD or user

---

## Writing for Junior Developers (and AI Agents)

- Be explicit and unambiguous
- Avoid jargon or explain it briefly
- Use consistent terminology from `prd.md`
- Number everything important for easy reference
- Prefer examples (payloads, UI states, edge cases) when it removes ambiguity

---

## Output

- **Format:** Markdown (`.md`)
- **Location:** `tasks/`
- **Filename:** `implementation-plan-[feature-name].md` (kebab-case)
- Title must be: `# Implementation Plan: [Feature Name]`

---

## Example (Mini)

```markdown
# Implementation Plan: Task Priority System

## Feature Summary
Add priority levels to tasks (high/medium/low) so users can focus on what matters most. Derived from PRD §Tasks → “Priorities”.

## Goals and Success Criteria
- Users can assign priority to a task in ≤ 2 clicks
- Priority persists and is visible in list views

## User Stories

### IP-001: Add priority field to database
**Description:** As a developer, I want to store task priority so it persists across sessions.

**Acceptance Criteria:**
- [ ] Add `priority` column to `tasks`: 'high' | 'medium' | 'low' (default 'medium')
- [ ] Migration runs successfully on a fresh DB and existing DB
- [ ] Typecheck/lint passes
- [ ] Tests updated/added for persistence

### IP-002: Display priority badge in task list
**Description:** As a user, I want to see task priority at a glance so I can decide what to do next.

**Acceptance Criteria:**
- [ ] Task rows/cards show a priority badge with label text (High/Medium/Low)
- [ ] Badge appears in list and detail view (if both exist)
- [ ] Typecheck/lint passes
- [ ] Verify in browser using dev-browser skill
```

---

## Checklist (Before Saving)

- [ ] Read `prd.md` and aligned terminology/scope
- [ ] Asked clarifying questions only where necessary
- [ ] Stories are small, sequenced, and implementable
- [ ] Acceptance criteria are verifiable and include dev-browser for UI
- [ ] Mapped requirements back to PRD sections
- [ ] Included testing + rollout + risks
- [ ] Saved to `tasks/implementation-plan-[feature-name].md`
