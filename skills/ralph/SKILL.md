---
name: ralph
description: "Convert an Implementation Plan (IP) to Ralph's JSON format for autonomous execution. Use when you have an existing IP and need to export it as ralph.json. Triggers on: convert this implementation plan, turn this IP into ralph format, create ralph.json from this, ralph json, export to ralph.json."
---

# Ralph IP Converter

Converts an existing **Implementation Plan** (markdown) into the `ralph.json` format that Ralph uses for autonomous execution.

---

## The Job

1. Read `prd.md` to understand project context (project name, terminology, constraints).
2. Take an **Implementation Plan** (markdown file or pasted text).
3. Convert its **User Stories** into a single `ralph.json` in your `tasks/` directory.

---

## Input Expectations

The Implementation Plan should include a **User Stories** section with items like:

- `### IP-001: Title` **or** `### IM-001: Title`
- Description: `As a [user], I want...`
- Acceptance Criteria as a checklist

If the IP is missing stories, or stories don’t have acceptance criteria, stop and ask for the correct IP.

---

## Output Format (ralph.json)

```json
{
  "project": "[Project Name]",
  "branchName": "ralph/[feature-name-kebab-case]",
  "description": "[Feature description from IP summary + PRD context]",
  "userStories": [
    {
      "id": "US-001",
      "title": "[Story title]",
      "description": "As a [user], I want [feature] so that [benefit]",
      "acceptanceCriteria": [
        "Criterion 1",
        "Criterion 2",
        "Typecheck passes"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    }
  ]
}
```

---

## Story Size: The Number One Rule

**Each story must be completable in ONE Ralph iteration (one context window).**

Ralph spawns a fresh Amp instance per iteration with no memory of previous work. If a story is too big, the LLM runs out of context before finishing and produces broken code.

### Right-sized stories
- Add a database column and migration
- Add a UI component to an existing page
- Update a server action with new logic
- Add a filter dropdown to a list

### Too big (split these)
- "Build the entire dashboard" → split: schema, queries, UI components, filters
- "Add authentication" → split: schema, middleware, login UI, session handling
- "Refactor the API" → split one story per endpoint or pattern

**Rule of thumb:** If you cannot describe the change in 2–3 sentences, it is too big.

---

## Story Ordering: Dependencies First

Stories execute in priority order. Earlier stories must not depend on later ones.

**Correct order**
1. Schema/database changes (migrations)
2. Server actions / backend logic
3. UI components that use the backend
4. Dashboard/summary views that aggregate data

**Wrong order**
1. UI component (depends on schema that does not exist yet)
2. Schema change

---

## Acceptance Criteria: Must Be Verifiable

Each criterion must be something Ralph can CHECK, not something vague.

### Good criteria (verifiable)
- "Add `status` column to tasks table with default 'pending'"
- "Filter dropdown has options: All, Active, Completed"
- "Clicking delete shows confirmation dialog"
- "Typecheck passes"
- "Tests pass"

### Bad criteria (vague)
- "Works correctly"
- "User can do X easily"
- "Good UX"
- "Handles edge cases"

### Always include as final criterion
```
"Typecheck passes"
```

For stories with testable logic, also include:
```
"Tests pass"
```

For stories that change UI, also include:
```
"Verify in browser using dev-browser skill"
```

---

## Conversion Rules (IP → ralph.json)

1. **Source of truth for stories = Implementation Plan**, not `prd.md`.
2. **Each IP user story becomes one JSON entry.**
3. **IDs in JSON**: sequential `US-001`, `US-002`, … (do not reuse `IP-xxx` / `IM-xxx`).
4. **Title**: use the IP story title (strip the `IP-xxx:` / `IM-xxx:` prefix).
5. **Description**: use the IP story description if present; otherwise derive it from title and PRD context.
6. **Acceptance Criteria**:
   - Convert checklist items (`- [ ] ...`) into plain strings.
   - Remove checkbox markers and formatting.
   - Ensure `"Typecheck passes"` is present (append if missing).
   - If the IP story implies UI changes and criteria doesn’t include it, append:
     - `"Verify in browser using dev-browser skill"`
   - If the story includes non-trivial logic and no tests mention, consider appending:
     - `"Tests pass"`
7. **Priority**:
   - Primary: dependency order (schema → backend → UI → aggregation).
   - Secondary: the order in the IP document.
8. **passes**: always `false`
9. **notes**: always `""`
10. **branchName**:
   - `ralph/[feature-name-kebab-case]`
   - Derive feature name from:
     - IP title: `# Implementation Plan: X` → `x` kebab-case
     - fallback: user-provided feature name
11. **project**:
   - Prefer explicit project name from `prd.md` (header/title).
   - If absent, infer from repo name (if provided by user) or ask.

---

## Splitting Stories When IP Is Too Big

If an IP story violates the one-iteration rule, split it **before** conversion.

Example:
**Original**
- "Add user notification system"

**Split into**
1. US-001: Add notifications table to database
2. US-002: Create notification service for sending notifications
3. US-003: Add notification bell icon to header
4. US-004: Create notification dropdown panel
5. US-005: Add mark-as-read functionality
6. US-006: Add notification preferences page

Each is one focused change that can be completed and verified independently.

---

## Example

**Input Implementation Plan (excerpt):**
```markdown
# Implementation Plan: Task Status Feature

## User Stories

### IP-001: Add status field to tasks table
**Description:** As a developer, I need to store task status in the database.

**Acceptance Criteria:**
- [ ] Add status column: 'pending' | 'in_progress' | 'done' (default 'pending')
- [ ] Generate and run migration successfully
- [ ] Typecheck passes
```

**Output ralph.json (excerpt):**
```json
{
  "project": "TaskApp",
  "branchName": "ralph/task-status-feature",
  "description": "Task Status Feature - Track task progress with status indicators",
  "userStories": [
    {
      "id": "US-001",
      "title": "Add status field to tasks table",
      "description": "As a developer, I need to store task status in the database.",
      "acceptanceCriteria": [
        "Add status column: 'pending' | 'in_progress' | 'done' (default 'pending')",
        "Generate and run migration successfully",
        "Typecheck passes"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    }
  ]
}
```

---

## Archiving Previous Runs

**Before writing a new `ralph.json`, check if there is an existing one from a different feature:**

1. Read the current `ralph.json` if it exists
2. Check if `branchName` differs from the new feature's branch name
3. If different AND `progress.txt` has content beyond the header:
   - Create archive folder: `archive/YYYY-MM-DD-feature-name/`
   - Copy current `ralph.json` and `progress.txt` to archive
   - Reset `progress.txt` with fresh header

**The ralph.sh script handles this automatically** when you run it, but if you are manually updating `ralph.json` between runs, archive first.

---

## Checklist Before Saving

- [ ] Read `prd.md` for project name/terminology
- [ ] Using IP as the story source of truth
- [ ] Each story is completable in one iteration (split if needed)
- [ ] Stories ordered by dependency (schema → backend → UI)
- [ ] Every story has `"Typecheck passes"`
- [ ] UI stories have `"Verify in browser using dev-browser skill"`
- [ ] Acceptance criteria are verifiable (not vague)
- [ ] No story depends on a later story
- [ ] Previous run archived if feature differs
