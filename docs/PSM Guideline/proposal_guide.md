# Proposal Writing Guidelines — PSM 1 Project Proposal

> Form reference: PSM.SPACE.CI.03

---

## Form Structure

The proposal form consists of the following sections:

| Section | Content |
|---|---|
| **A** | Student information |
| **B** | Project details (the main written content) |
| **C** | Student acknowledgement |
| **D** | Supervisor acknowledgement |
| **F** | For SC SPACE coordinator use only |

---

## Section B — Project Details

### Problem Background and Proposed Solution

- Describe the **current problem** or gap that motivates the project.
- Explain the **proposed solution** — what the system will do to address the problem.
- Write in prose (paragraphs), not bullet points.
- Keep it focused: state the problem clearly, then state how the system solves it.

---

### Objectives

Objectives must follow the **SMART** criteria:

| Criterion | Meaning |
|---|---|
| **Specific** | Each objective must be specific — what exactly will be done? |
| **Measurable** | The success of each objective must be measurable. |
| **Achievable** | Must be realistically accomplishable. |
| **Realistic & Relevant** | Must be appropriate and relevant to the project. |
| **Timely** | Must be completable within the given timeframe. |

**Format rules:**
- Written as a numbered list.
- Each objective starts with an action verb in the infinitive: *To study …*, *To design …*, *To develop …*, *To test …*
- Each objective describes a **task** that contributes toward achieving the project goal, aligned with the chosen development methodology.
- Must be clearly stated and measurable (i.e. the level of success of each task can be determined).

**Example:**
> The objectives of the project are:
> 1. To study, collect, and analyse the requirements for developing a knowledge mapping web application.
> 2. To design the system architecture and interface for the knowledge mapping web application.
> 3. To develop and test the knowledge mapping web application based on the stated requirements.

---

### Scope

The scope defines the **boundary, constraints, and limitations** of the system. It answers:

- **What** — what data is used? what is covered?
- **Who** — who are the target users?
- **Where** — what is the running platform? (web-based, mobile — iOS/Android, etc.)

**Format rules:**
- Written as a numbered list.
- State the **number of modules** the system consists of and list them in a **sub-numbered form** (must tally with the Use Case Diagram).
- Each point should be a clear, standalone statement.

**Example:**
> The scope of the system is as follows:
> 1. The application will be a web-based system accessible via a web browser.
> 2. The target users of this system are business owners, supervisors, and staff members within a single organisation.
> 3. The application will support one language only: English.
> 4. This system will consist of five (5) modules:
>    - 4.1 Authentication and Role Management
>    - 4.2 Skill Assessment
>    - 4.3 Visualisation Dashboard
>    - 4.4 AI Trend Pipeline
>    - 4.5 Gap Analysis and Reporting

---

### Project Requirements

Filled in as a table with the following fields:

| Field | Description |
|---|---|
| **Software** | Development tools, IDEs, frameworks, database software |
| **Hardware** | Operating system, RAM, storage requirements |
| **Technology / Technique / Method / Algorithm** | Development methodology (e.g. Agile) and key techniques |
| **Network Elements** | Server, database, client-side infrastructure |
| **Security Elements** | Authentication, encryption, access control measures |
| **Project Type** | Circle one: *System Development* or *Research* |
| **Project Area** | e.g. *Data Analytics – Workforce Intelligence* |

**Notes on network and security elements:**
- **Network:** Full system integration between client/server (web, database, etc.); no local-host-only deployment (must be accessible beyond localhost for v1 cloud deployment); admin features must be complete.
- **Security:** Password and database encryption are mandatory; include flow control, access control, and logical security measures appropriate to the application's purpose.

---

## Common Mistakes to Avoid

- Objectives that are vague or not measurable — e.g. *"To create a system"* without specifying what kind or for what purpose.
- Scope points that overlap with objectives — scope defines **what the system covers**, objectives define **what you will do**.
- Listing modules in scope that do not correspond to the Use Case Diagram.
- Writing the problem background in bullet points instead of prose.
- Using informal language or first-person pronouns — refer to the General Writing Guidelines.