---
agent: agent
---

# PR Description Generator

Generate a comprehensive and well-structured Pull Request description by analyzing git changes and optionally integrating Jira ticket context.

## Workflow

### Step 1: Analyze Changes

1. Check git differences between current branch and `develop`
2. Identify modified files and their changes
3. Extract current branch name to determine ticket ID (format: `feature-app/[TICKET-ID]`)

### Step 2: Request Jira Context

1. Ask the developer: **"Please provide the Jira ticket URL (or press Enter to skip):"**
2. **If Jira URL is provided:**
   - Fetch ticket summary and description using available tools
   - Use ticket context + code changes to formulate Description and Problem/Context sections
3. **If no Jira URL:**
   - Generate Description and Problem/Context based solely on code changes analysis

### Step 3: Generate PR Description

Follow this template:

```markdown
## 🎫 Ticket

[Jira ticket URL or "N/A"]

## 📝 Description

[Brief 1-2 sentence summary combining ticket context (if available) and code changes]

## 🐛 Problem / Context

[Concise explanation of the problem being solved, informed by ticket + changes]

## ✅ Solution

[Brief, concise, and highly technical explanation of the solution applied. Focus on implementation details, architectural decisions, and key technical changes. Keep this section short but precise.]

## 📸 Screenshots / Videos

[Add visual evidence if UI changes are involved]

- Before: [Screenshot/video]
- After: [Screenshot/video]
```

### Step 4: Present Proposal

1. Display the complete PR description to the developer
2. Ask: **"Approve this PR description? (yes/edit/cancel)"**

### Step 5: Handle Feedback

- **If "yes"**: Proceed to Step 6
- **If "edit"**: Ask what needs to be changed, re-evaluate, and regenerate. Return to Step 4
- **If "cancel"**: Abort process

### Step 6: Create Temporary File

1. Extract ticket ID from branch name (e.g., `feature-app/EEC-3296` → `EEC-3296`)
2. Create file at `.prs/[TICKET-ID].md` with the approved PR description
3. Generate PR title from Description section (max 72 characters)

### Step 7: Create PR via GitHub CLI

Execute:

```bash
gh pr create --base develop --title "[PR_TITLE]" --body-file .prs/[TICKET-ID].md
```

### Step 8: Cleanup

- **If PR created successfully:**
  - Delete temporary file: `rm .prs/[TICKET-ID].md`
  - Display PR URL to developer
  - Process complete ✅
- **If error occurs:**
  - Keep the `.prs/[TICKET-ID].md` file
  - Notify developer: "❌ Unable to create PR automatically. Please create it manually using the file at `.prs/[TICKET-ID].md`"

## Guidelines

1. **Solution section must be technical**: Focus on implementation specifics, not general descriptions
2. **Be concise**: Avoid unnecessary verbosity in all sections
3. **Use bullet points**: For clarity and scannability
4. **File references**: Use relative paths and link to specific files when mentioning them
5. **Emojis**: Only use the ones in the template section headers

## Important Notes

- Always target `develop` as base branch
- Extract ticket ID from branch name following the `feature-app/[TICKET-ID]` convention
- The Solution section should be the most technical and concise part
- Allow iterative editing before final approval
- Handle errors gracefully and provide clear next steps
