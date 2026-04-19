# Jamie Lai — Supplementary CV Detail

This file provides additional context for the resume agent.
Read this file when profile.yaml needs clarification or more detail.

---

## Education

**University of California, Santa Barbara** — M.S. in Computer Science | GPA: 3.8 / 4.0
Sept 2023 – Dec 2025 | Santa Barbara, CA

**National Chung Hsing University** — M.B.A in Management Information System | GPA: 4.1 / 4.3
Sept 2020 – Jun 2022 | Taichung, Taiwan

**National Taichung University of Science and Technology (Elite Program)**
Bachelor of Information Management | GPA: 3.9 / 4.0
Sept 2018 – Jun 2020 | Taichung, Taiwan

---

## Additional Context on Experience

### UCSB Graduate Research Assistant
- The research investigates LLM-based multi-robot task planning in household
  environments simulated with AI2-THOR
- We designed and compared memory representations using structured JSON, evaluating
  two approaches: summary-based memory (condensed natural language summaries of past
  states) and log-based memory (append-only records of individual events and
  observations)
- We also compared centralized planning (a single coordinator agent) against
  decentralized planning (each robot reasons independently), resulting in four
  architecture combinations tested across multiple task scenarios
- Key finding: centralized log-based memory achieved the highest task completion
  rate overall; decentralized log-based memory remained competitive with centralized
  summary-based memory despite the additional challenges introduced by decentralization
- Conclusion: the choice of textual memory representation in LLM-based robotic
  reasoning has a substantial and measurable impact on task performance,
  independent of the planning architecture
- Paper is under review at IROS 2026 (top-tier robotics conference)

### Topia, Inc. — Software Engineer Intern
- Topia, Inc. is an early-stage startup, which meant requirements were often
  ambiguous, priorities shifted quickly, and decisions needed to be made with
  incomplete information
- Developed strong ability to adapt to unclear or evolving requirements —
  clarifying scope proactively, making reasonable assumptions when needed,
  and iterating quickly based on stakeholder feedback
- The fast-paced startup environment required balancing execution speed with
  code quality, and communicating trade-offs clearly to non-technical founders
- Company website is live at https://the-topia.com/
- The 30% reduction in development time came from the modular component
  architecture introduced during the project

### TSMC / NCHU Research Assistant
- Jetson Nano is NVIDIA edge computing hardware deployed at the factory floor
- The system ran in a real factory environment detecting PPE compliance in real time
- F1: 97%, mAP: 99.77%, IoU: 0.86 are validated production metrics from the
  deployed system

### ShangDa Plastic — Software Developer
- MIS = Manufacturing Information System
- The 50% efficiency gain is measured against the previous manual data entry process
- The system was deployed on AWS EC2 with MySQL as the primary database

---

## Resume Agent Project
- This project is the resume generation tool contained in this repository
- The two-layer architecture refers to CLAUDE.md (system logic) and
  profile.yaml (user data) being intentionally separated so that workflow
  rules and personal data can evolve independently
- Human-in-the-loop means the agent pauses at three points during generation
  to ask the user to confirm or correct the selected content before proceeding