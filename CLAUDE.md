# Resume Agent — CLAUDE.md

This file defines the complete operating instructions for Claude Code when working in this repository. Read this file in full before executing any task.

---

## What This Repo Does

This tool generates a tailored LaTeX resume for a specific job application. Given a job description (JD) as text or URL, it selects the most relevant content from the user's profile, rewrites the three editable sections of theLaTeX template, and pushes the result to Overleaf via Git.

---

## File Structure and Responsibilities

| File | Layer | Purpose |
|---|---|---|
| `CLAUDE.md` | System | Workflow rules and operating instructions (this file) |
| `data/profile.yaml` | User | Single source of truth for all personal background data |
| `data/cv.md` | User | Supplementary detail — read this if profile.yaml needs clarification |
| `templates/example.tex` | Template | Base LaTeX template — contains AGENT section markers |
| `output/` | Generated | All generated .tex files go here — never modify manually |
| `data/applications/applications.csv` | Tracking | One row per application, append only |
| `data/applications/applications.md` | Tracking | Human-readable version of applications.csv |

---

## How to Read the User's Profile

Before executing any task, read `data/profile.yaml` in full.
If any experience or project entry needs more context, refer to `data/cv.md`.

The profile is the **only** source of content for resume generation.
Do not invent, infer, or paraphrase any experience, skill, or metric
that does not exist in the profile. If something is not in the profile,
it does not go into the resume.

---

## The Section Marker System

The file `templates/example.tex` contains three editable regions marked with
special LaTeX comments in this exact format:

```
% ===AGENT:START:SKILLS===
...editable content...
% ===AGENT:END:SKILLS===
```

The three markers are: `SKILLS`, `EXPERIENCE`, `PROJECTS`.

**Hard rules for LaTeX editing:**
- You may ONLY modify content between a START and its matching END marker
- Never move, delete, or rewrite the marker comment lines themselves
- Never modify anything outside of a marked region — this includes the
  preamble, document settings, header section, Education section,
  and all \newcommand definitions
- The output .tex file must compile without errors
- Preserve all LaTeX commands and syntax exactly —
  use \resumeItem, \resumeSubheading, \resumeProjectHeading etc.
  exactly as they appear in the template
- Do not add any new \usepackage or \newcommand declarations

---

## Workflow: Resume Generation

When the user says "generate a resume for [JD text or URL]", execute the
following steps in order. Do not skip steps. Do not combine steps.

### Step 1 — Fetch the JD

If the input is a URL, fetch the page content and extract the job description
text. If the input is plain text, use it directly.
Confirm to the user: "JD fetched. Role: [role title] at [company name]."

### Step 2 — Parse the JD

Extract and structure the following from the JD text:
- `company_name`
- `role_title`
- `required_skills` (list)
- `nice_to_have_skills` (list)
- `responsibilities` (list of key themes)
- `keywords` (important terms that should appear in the resume)
- `role_type`: classify as one of — ai_engineer, software_engineer,
  fullstack_engineer, frontend_engineer, backend_engineer, research, other

Print the parsed JD summary to the terminal in a clean readable format.

### Step 3 — Match Profile to JD

Read `data/profile.yaml` and `data/cv.md` in full before making any selections.

**Content rules — apply to all three sections:**
- All content must be grounded in `profile.yaml` or `cv.md`
- You may rewrite or rephrase descriptions to better match the JD's tone,
  keywords, and emphasis — this is encouraged
- You may not introduce any technology, tool, metric, or experience
  that does not appear anywhere in `profile.yaml` or `cv.md`
- You may not fabricate or estimate any numbers or percentages
  that are not already stated in the source files

**EXPERIENCE:**
- Include all experience entries
- Order strictly by date, most recent first — do not reorder by relevance
- Each entry: minimum 2, maximum 4 bullet points
- Rewrite bullets to emphasize aspects most relevant to the JD
  while staying grounded in the source content

**SKILLS:**
- All skills listed must exist in `profile.yaml` or `cv.md`
- You may reorganize the categories and their names to better fit the JD
  (e.g. rename "AI/ML" to "AI & LLM Tools" if more appropriate)
- You may reorder categories so the most JD-relevant ones appear first
- You may omit skills that are clearly irrelevant to the JD
- You may not add any skill not found in the source files

**PROJECTS:**
- Select exactly 1 project most relevant to the JD
- Maximum 3 bullet points
- Rewrite bullets to emphasize aspects most relevant to the JD
  while staying grounded in the source content

---

### Step 4 — Human Review: Content Selection

Review each section separately in the following order.
Do not proceed to the next section until the user confirms the current one.

**Section 1 of 3 — EXPERIENCE**

Print the full experience content in this format:=== REVIEW 1/3 — EXPERIENCE ===

[Role] at [Company] ([Period])

[bullet 1]
[bullet 2]
[bullet 3]


[Role] at [Company] ([Period])

[bullet 1]
[bullet 2]
...



================================Then ask:
"Does the Experience section look correct?
Type YES to continue, or describe what you want to change."

Wait for response. If the user requests changes, apply and reprint.
Repeat until YES.

---

**Section 2 of 3 — SKILLS**

Print the full skills content in this format:=== REVIEW 2/3 — SKILLS ===
[Category 1]: [skill1], [skill2], [skill3]
[Category 2]: [skill1], [skill2]
...
===========================Then ask:
"Does the Skills section look correct?
Type YES to continue, or describe what you want to change."

Wait for response. If the user requests changes, apply and reprint.
Repeat until YES.

---

**Section 3 of 3 — PROJECTS**

Print the selected project in this format:=== REVIEW 3/3 — PROJECTS ===
[Project Name] ([Period])

[bullet 1]
[bullet 2]
[bullet 3]

=============================Then ask:
"Does the Projects section look correct?
Type YES to proceed to LaTeX generation, or describe what you want to change."

Wait for response. If the user requests changes, apply and reprint.
Repeat until YES.

Only after all three sections are confirmed, proceed to Step 5.

### Step 5 — Write the LaTeX

Read `templates/example.tex`.
Replace the content inside each AGENT marker region with the confirmed
selections, formatted using the correct LaTeX commands from the template.

Generate the output filename using this format:
`{CompanyName}_{RoleTitle}_{YYYYMMDD}.tex`
- Remove spaces and special characters from company and role names
- Use today's date
- Example: `Google_AIEngineer_20260419.tex`

Save the new file to `output/{filename}`.
Do not overwrite `templates/example.tex`.

### Step 6 — Human Review: Final Check Before Push

Print to the terminal:

```
=== READY TO PUSH ===
Output file : output/{filename}
Commit msg  : "Add {company} {role} resume – {date}"
Overleaf    : {OVERLEAF_GIT_URL from .env}
=====================
```

Then ask:
"Ready to push to Overleaf?
Type YES to push, or NO to cancel."

Wait for the user's response. If NO, stop here and tell the user where
the output file is saved locally.

### Step 7 — Push to Overleaf

Execute the following git operations in order:

1. Read OVERLEAF_GIT_URL and OVERLEAF_GIT_TOKEN from `.env`
2. Check if the Overleaf repo is already cloned locally at `.overleaf/`
   - If not: `git clone https://{OVERLEAF_GIT_TOKEN}@{OVERLEAF_GIT_URL_WITHOUT_HTTPS} .overleaf`
   - If yes: `cd .overleaf && git pull`
3. Check if the folder `.overleaf/agent/` exists
   - If not: `mkdir .overleaf/agent`
4. Copy `output/{filename}` into `.overleaf/agent/{filename}`
5. `cd .overleaf`
   `git add agent/{filename}`
   `git commit -m "Add {company} {role} resume – {date}"`
   `git push`
6. Capture the commit hash from the git output

Also add `.overleaf/` to `.gitignore` if not already present.

After pushing, print:
"Pushed successfully to Overleaf under the agent/ folder.
Open Overleaf to preview: {OVERLEAF_GIT_URL}"

### Step 8 — Update Tracking Records

Append one row to `data/applications/applications.csv`:
```
{date},{company},{role},{jd_source},{filename},{commit_hash},
```

Append one row to the table in `data/applications/applications.md`:
```
| {date} | {company} | {role} | {jd_source} | {filename} | {commit_hash} | |
```

Print: "Tracking records updated."

---

## What NOT to Do

- Do not modify `templates/example.tex` directly — it is the base template
- Do not modify the Education section of any generated file
- Do not invent metrics, skills, or experience not found in `profile.yaml`
- Do not push to Overleaf without explicit YES confirmation from the user
- Do not overwrite an existing file in `output/` — always generate a new filename
- Do not modify `data/profile.yaml` during resume generation

---

## How to Update the Profile

If the user asks to update their profile data, edit `data/profile.yaml`
directly. Do not modify `CLAUDE.md` for profile changes.
After editing, summarize what was changed and ask the user to confirm.