# Resume Agent

A Claude Code-powered CLI agent that tailors a LaTeX resume to a specific job description — automatically, with human review built in.



## What This Is

Applying for jobs means rewriting the same resume over and over: adjusting bullet points, reordering skills, picking the right projects. This tool automates that process.

Given a job description (as plain text or a URL), the agent reads your personal profile, selects the most relevant experience and skills, rewrites the content to match the JD's tone and keywords, and pushes a new tailored `.tex` file directly to your Overleaf project — ready for your final review.

This is a personal tool built to solve a real problem. It is also designed as an AI engineering portfolio project, demonstrating agent workflow design, human-in-the-loop review, tool use, and a clean separation between system logic and user data.



## How It Works

The system is built on two layers:

**System Layer — `CLAUDE.md`**
Defines the full workflow: how to parse a JD, how to select content, what LaTeX sections can be modified, when to pause for human review, and how to push to Overleaf. This file is the agent's operating instructions.

**User Layer — `data/profile.yaml` and `data/cv.md`**
Your personal source of truth. All experience, skills, and projects live here. The agent selects and rewrites from this data — it never fabricates content that isn't already in these files.

The LaTeX template uses section markers to define the three regions the agent is allowed to modify:

```latex
% ===AGENT:START:SKILLS===
...
% ===AGENT:END:SKILLS===
```

Everything outside these markers — the preamble, header, Education section, and all formatting commands — is never touched.



## Tools Used

| Tool | Purpose |
|---|---|
| Claude Code | The agent runtime — executes the full workflow via CLI |
| LaTeX / Overleaf | Resume template format and preview environment |
| Git | Pushes generated `.tex` files to Overleaf |
| YAML | Structured personal profile format |



## Repo Structure

```
resume-agent/
├── CLAUDE.md                        # System layer — agent workflow and rules
├── data/
│   ├── profile.yaml                 # User layer — your personal profile data
│   ├── cv.md                        # Supplementary context for the agent
│   └── applications/
│       ├── applications.csv         # Tracking log of all applications
│       └── applications.md          # Human-readable version of the log
├── templates/
│   └── swe.tex                      # Base LaTeX template with section markers
└── output/                          # All generated .tex files land here
```



## How to Use

**Prerequisites**
- [Claude Code](https://claude.ai/code) installed and authenticated
- A paid Overleaf account with Git integration enabled
- Your Overleaf Git URL and personal access token

**Setup**

1. Clone this repo:
```bash
git clone https://github.com/jamie870116/resume-agent.git
cd resume-agent
```

2. Copy the example env file and fill in your credentials:
```bash
cp .env.example .env
```

```
ANTHROPIC_API_KEY=your_key_here
OVERLEAF_GIT_URL=https://git.overleaf.com/your_project_id
OVERLEAF_GIT_TOKEN=your_overleaf_token
```

3. Copy your LaTeX template into `templates/swe.tex` and make sure it contains the AGENT section markers.

4. Fill in `data/profile.yaml` with your personal background data. Use the structure already in the file.

**Running the Agent**

Open Claude Code in the repo root and run:

```bash
claude "Generate a tailored resume for this job: [paste JD text or URL here]"
```

The agent will:
1. Fetch and parse the JD
2. Match your profile to the JD requirements
3. Pause and ask you to review the selected Experience — confirm or request changes
4. Pause and ask you to review the selected Skills — confirm or request changes
5. Pause and ask you to review the selected Project — confirm or request changes
6. Write a new `.tex` file to `output/`
7. Ask for final confirmation before pushing to Overleaf
8. Push to your Overleaf `agent/` folder and update the tracking log



<!-- ## Design Decisions

**Why Claude Code instead of a Python script?**
Claude Code already has built-in tool use — file reading, web fetching, shell execution. There is no need to reimplement these capabilities. The workflow logic lives in `CLAUDE.md` rather than code, which makes it easy to read, modify, and reason about.

**Why YAML for the profile?**
YAML is structured enough for the agent to query selectively, and human-readable enough for you to maintain without any tooling. The agent reads only what it needs rather than loading the entire profile into every prompt.

**Why section markers in the LaTeX?**
To make the agent's permitted edit boundaries explicit and machine-readable. Without markers, the agent would have to infer boundaries from LaTeX structure — which introduces risk. With markers, the constraint is hardcoded in the file itself.

--- -->

## Tracking Applications

Every successful push appends a row to `data/applications/applications.csv` and `data/applications/applications.md`, recording the date, company, role, JD source, output filename, and Overleaf commit hash. This gives you a lightweight log of every application you have sent out.
