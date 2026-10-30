# GitHub Copilot CLI: From Installation to Custom AI Agents

> A practical, presentation-ready guide to installing GitHub Copilot CLI, authenticating, exploring its terminal interface, creating custom agents, and using them in a repository.

**Audience:** Developers, students, and GitHub users  
**Platform used in this walkthrough:** macOS with zsh and Homebrew  
**Last reviewed:** 30 October 2026

---

## Table of contents

1. [What is GitHub Copilot CLI?](#1-what-is-github-copilot-cli)
2. [GitHub CLI (`gh`) vs Copilot CLI (`copilot`)](#2-github-cli-gh-vs-copilot-cli-copilot)
3. [Prerequisites](#3-prerequisites)
4. [Install Copilot CLI on macOS](#4-install-copilot-cli-on-macos)
5. [Authenticate and start a session](#5-authenticate-and-start-a-session)
6. [Explore the interactive terminal](#6-explore-the-interactive-terminal)
7. [Understand agents](#7-understand-agents)
8. [Create a custom agent using `/agent`](#8-create-a-custom-agent-using-agent)
9. [Create an agent manually](#9-create-an-agent-manually)
10. [Use and select your custom agent](#10-use-and-select-your-custom-agent)
11. [Build a small specialist agent team](#11-build-a-small-specialist-agent-team)
12. [A practical live-demo sequence](#12-a-practical-live-demo-sequence)
13. [Useful commands and shortcuts](#13-useful-commands-and-shortcuts)
14. [Troubleshooting](#14-troubleshooting)
15. [Security and responsible use](#15-security-and-responsible-use)
16. [Key concepts to explain during the presentation](#16-key-concepts-to-explain-during-the-presentation)
17. [Official references](#17-official-references)
18. [Quick-start cheat sheet](#quick-start-cheat-sheet)
19. [Everyday GitHub Copilot CLI: `@` `/` `#`](#everyday-github-copilot-cli---)
20. [How to Create `copilot-instructions.md`](#how-to-create-copilot-instructionsmd)

---

## 1. What is GitHub Copilot CLI?

GitHub Copilot CLI is GitHub Copilot's terminal-based AI coding assistant. It lets you work with a repository, ask questions about code, plan changes, write and review code, and interact with supported tools without leaving the terminal.

It is an **agentic** assistant: depending on the task and permissions, it can inspect files, use tools, run commands, and make changes. Review proposed actions and grant only the permissions needed.

### Why use it?

- Work directly in the terminal and current repository.
- Ask questions about an unfamiliar codebase.
- Plan, implement, debug, and review changes.
- Create custom agents for repeatable specialist tasks.
- Delegate focused work to subagents when appropriate.
- Use Copilot interactively or from scripts.

> **Important:** Copilot CLI is not the same thing as the older GitHub CLI Copilot extension. This guide covers the standalone `copilot` CLI and its `/agent` feature.

## 2. GitHub CLI (`gh`) vs Copilot CLI (`copilot`)

These are two different command-line tools.

| Command   | Tool               | Main purpose                                                                  |
| --------- | ------------------ | ----------------------------------------------------------------------------- |
| `gh`      | GitHub CLI         | Work with GitHub repositories, issues, pull requests, releases, and workflows |
| `copilot` | GitHub Copilot CLI | AI-assisted coding, repository analysis, tool use, and custom agents          |
| `git`     | Git                | Version control: commits, branches, merges, and local repository history      |

You may use all three in the same development workflow, but installing `gh` is not a prerequisite for installing the standalone Copilot CLI.

## 3. Prerequisites

Before installation, make sure you have:

- A GitHub account.
- Access to a GitHub Copilot plan that includes Copilot CLI.
- A terminal (Terminal or iTerm2 on macOS).
- Internet access for installation and authentication.
- Homebrew for the Homebrew installation route.

If Copilot is provided by a school, company, or organization, an administrator may need to enable Copilot CLI access.

### Check whether Homebrew is installed

```bash
brew --version
```

If the command is not found, install Homebrew using the official instructions:

<https://brew.sh/>

Then open a new terminal and verify again:

```bash
brew --version
```

### Optional: check Node.js

Node.js 22 or later is required if you choose the npm installation route:

```bash
node --version
npm --version
```

You do not need to install Node.js separately when using the Homebrew cask route.

## 4. Install Copilot CLI on macOS

### Option A — Homebrew (recommended for this walkthrough)

```bash
brew install --cask copilot-cli
```

### Option B — npm

Requires Node.js 22 or later:

```bash
npm install -g @github/copilot
```

### Option C — official install script

```bash
curl -fsSL https://gh.io/copilot-install | bash
```

Use one installation method, rather than installing the same CLI through several package managers.

### Verify the installation

```bash
copilot --version
```

If the version command is unavailable in your installed release, check the CLI help:

```bash
copilot help
```

## 5. Authenticate and start a session

### Step 1 — Open your project folder

For example:

```bash
mkdir copilot-agent-demo
cd copilot-agent-demo
```

If you already have a repository, navigate to its directory instead:

```bash
cd path/to/your/repository
```

### Step 2 — Start Copilot CLI

```bash
copilot
```

### Step 3 — Sign in

Inside the Copilot interactive interface, enter:

```text
/login
```

Follow the browser/device authentication instructions and sign in with the GitHub account that has Copilot access.

You normally only need to authenticate once on a machine.

### Step 4 — Trust the working directory

If Copilot asks whether you trust the current folder, inspect the folder and confirm only if it is a project you trust. Do not blindly trust unknown repositories.

### Step 5 — Test it

Try:

```text
Give me an overview of this project. Explain its folder structure and identify the main technologies.
```

For an empty demo folder, try:

```text
Explain what files and folders you recommend for a small beginner-friendly web application. Do not create anything yet.
```

## 6. Explore the interactive terminal

The interactive interface supports slash commands and shortcuts.

### Useful shortcuts

| Input      | Purpose                                                     |
| ---------- | ----------------------------------------------------------- |
| `/`        | Open the slash-command menu                                 |
| `?`        | Show tabbed help                                            |
| `@`        | Reference files in the prompt                               |
| `Esc`      | Cancel the current operation                                |
| `Ctrl + C` | Cancel, clear input, or exit depending on the current state |
| `Ctrl + L` | Clear the terminal screen                                   |
| `↑` / `↓`  | Navigate command history                                    |

For the complete command list in your installed version, use:

```text
/help
```

### A few useful prompt examples

```text
Explain the purpose of the src directory.
```

```text
Find the entry point of this application and explain the execution flow.
```

```text
Review the current Git changes. Do not edit any files.
```

```text
Create a plan to add input validation. Do not implement it yet.
```

### Non-interactive mode

You can also run a one-off prompt without entering the interactive interface:

```bash
copilot -p "Explain the purpose of this repository."
```

Use `-s` to request only the response text:

```bash
copilot -sp "Summarize the latest changes in this repository."
```

For the full set of supported flags, check:

```bash
copilot help
```

## 7. Understand agents

An **agent** is a configured Copilot assistant with a defined role, expertise, instructions, and optionally a restricted set of tools.

For example:

- A Security Auditor focuses on security analysis.
- A Code Reviewer focuses on reviewing changes.
- A Documentation Writer focuses on Markdown documentation.
- A Test Engineer focuses on test cases and test execution.

A custom agent is not automatically a separate, always-running AI service or a separate model. It is a specialist configuration that Copilot can select or delegate work to. Subagents can work in their own context, while the main Copilot session coordinates the larger task.

### Built-in agents and custom agents

Copilot CLI includes built-in specialist agents in supported versions. Examples documented by GitHub include Explore, Task, Plan, and Code-review. Custom agents are the profiles you create for your own workflows.

The `/agent` menu is for choosing available agents; built-in agents may not appear in the custom-agent list.

## 8. Create a custom agent using `/agent`

This is the main demonstration in this guide.

### Step 1 — Start Copilot in your repository

```bash
cd copilot-agent-demo
copilot
```

### Step 2 — Open the agent menu

Inside the interactive Copilot session:

```text
/agent
```

### Step 3 — Start creating an agent

In the agent menu:

1. Press `N` to create a new agent.
2. Choose where to save it:
   - **Project** — `.github/agents/`
   - **User** — `~/.copilot/agents/`
3. Choose whether Copilot should generate the agent profile or whether you want to create it manually.

### Project agent vs User agent

| Scope   | Location             | When to use it                                                                        |
| ------- | -------------------- | ------------------------------------------------------------------------------------- |
| Project | `.github/agents/`    | The agent belongs to this repository and can be shared with collaborators through Git |
| User    | `~/.copilot/agents/` | The agent is personal and available across your repositories                          |

For a team presentation or shared repository, choose **Project**.

### Step 4 — Ask Copilot to create the profile

Choose the option to create the agent with Copilot, then describe its role and behavior.

Use this example:

```text
Create a custom agent named Web Security Auditor.

Its role is to act as a careful web application security code reviewer.

Responsibilities:
- Review application source code for common web security weaknesses.
- Look for SQL injection, cross-site scripting, insecure direct object references,
  broken access control, authentication weaknesses, CSRF, and insecure configuration.
- Inspect API routes, authorization checks, input validation, and database queries.
- Explain the root cause and impact of each confirmed finding.
- Recommend secure fixes and show corrected code snippets when useful.
- Produce a structured Markdown security assessment report.

Rules:
- Work only on repositories and systems that I own or have explicit permission to assess.
- Begin with read-only analysis.
- Do not attack or exploit production systems.
- Do not delete data, expose secrets, or modify application files unless I explicitly approve.
- Do not claim a vulnerability is confirmed without supporting evidence.
- Separate confirmed findings from potential risks.
- Include severity, affected file or component, evidence, impact, and remediation.
```

### Step 5 — Review the generated profile

Copilot provides options such as:

- **Continue** — accept the generated profile.
- **Review content** — open and inspect the profile.
- **Try again** — request another version.
- **Quit** — leave the creation flow.

Review the instructions and tools before continuing. Keep the agent's permissions as narrow as practical.

### Step 6 — Restart Copilot CLI

After completing creation, restart the CLI so the new profile is loaded:

```text
/exit
```

Then start it again from the repository:

```bash
copilot
```

Open the agent menu:

```text
/agent
```

Your new custom agent should now be available.

## 9. Create an agent manually

You can create the same kind of agent without the interactive wizard.

### Step 1 — Create the project agent directory

Run this in the root of your Git repository:

```bash
mkdir -p .github/agents
```

Explanation:

- `mkdir` creates a directory.
- `-p` creates missing parent directories and does not fail if the directory already exists.
- `.github` is a hidden directory commonly used for GitHub repository configuration.
- `agents` stores custom agent profiles.

### Step 2 — Create the agent file

```bash
touch .github/agents/web-security-auditor.agent.md
```

### Step 3 — Open it in VS Code (optional)

```bash
code .github/agents/web-security-auditor.agent.md
```

If the `code` command is not available, open the file using your preferred editor.

### Step 4 — Add the agent profile

Paste the following content into the file:

```markdown
---
name: web-security-auditor
description: >
  Reviews web application source code for security weaknesses.
  Use when a security audit, security review, or vulnerability
  assessment of repository code is requested.
---

You are a careful web application security code reviewer.

Your responsibilities:

- Inspect source code, routes, controllers, middleware, API handlers,
  database queries, authentication, and authorization logic.
- Look for SQL injection, XSS, IDOR, broken access control, CSRF,
  insecure session handling, exposed secrets, and unsafe configuration.
- Explain the root cause and practical impact of each finding.
- Recommend secure fixes and provide corrected code examples.
- Produce a clear Markdown report.

Rules:

- Assess only code and systems the user owns or is authorized to test.
- Start with read-only inspection.
- Do not exploit production systems.
- Do not modify files unless the user explicitly approves.
- Never print, copy, or expose secret values.
- Do not invent evidence. Clearly label uncertainty.
- Separate confirmed vulnerabilities from potential concerns.

For each finding, include:

1. Title
2. Severity and rationale
3. Affected file and relevant code location
4. Evidence and explanation
5. Potential impact
6. Recommended remediation
7. Suggested validation or regression test
```

The `.agent.md` extension is important: it identifies the file as a custom agent profile.

### Step 5 — Restart and select it

```bash
copilot
```

Inside the CLI:

```text
/agent
```

Select **web-security-auditor**.

## 10. Use and select your custom agent

There are several ways to use a custom agent.

### Method A — Select from `/agent`

```text
/agent
```

Choose `web-security-auditor`, then enter:

```text
Review the authentication module in this project.
Identify possible security weaknesses.
Do not modify any files. Give me a Markdown report.
```

### Method B — Ask Copilot to use the agent by name

In the default Copilot session:

```text
Use the web-security-auditor agent to review the authentication
and authorization logic in the src directory. Do not modify files.
```

Copilot can use the agent if it determines the task matches the agent's description.

### Method C — Run the agent from the shell

The custom agent ID is normally derived from the profile filename without `.agent.md`.

```bash
copilot --agent web-security-auditor --prompt "Review the authentication module. Do not modify files."
```

This is useful for repeatable commands and scripts.

## 11. Build a small specialist agent team

Once the first agent works, create more specialist profiles.

Suggested project structure:

```text
copilot-agent-demo/
├── .github/
│   └── agents/
│       ├── web-security-auditor.agent.md
│       ├── code-reviewer.agent.md
│       ├── test-engineer.agent.md
│       └── documentation-writer.agent.md
├── src/
├── tests/
└── README.md
```

### Agent 1 — Web Security Auditor

**Goal:** Identify and explain potential security issues.

Example task:

```text
Use the web-security-auditor agent to inspect the login and registration
code. Work read-only and report evidence, severity, impact, and fixes.
```

### Agent 2 — Code Reviewer

Create `.github/agents/code-reviewer.agent.md`:

```markdown
---
name: code-reviewer
description: >
  Reviews code changes for correctness, maintainability, regressions,
  and consistency with the project conventions.
---

You are a careful code reviewer.

- Inspect changed files and relevant surrounding code.
- Prioritize bugs, regressions, edge cases, and maintainability problems.
- Explain why each issue matters and how to fix it.
- Do not rewrite code unless explicitly asked.
- Do not report style preferences as bugs.
- Do not invent findings.
```

### Agent 3 — Test Engineer

Create `.github/agents/test-engineer.agent.md`:

```markdown
---
name: test-engineer
description: >
  Designs and runs focused tests for application features and bug fixes.
---

You are a software test engineer.

- Inspect the feature and its expected behavior.
- Identify normal, boundary, invalid-input, and failure cases.
- Recommend focused unit and integration tests.
- Run tests only when the user authorizes command execution.
- Do not delete data or alter production environments.
- Summarize test results and any untested cases.
```

### Agent 4 — Documentation Writer

Create `.github/agents/documentation-writer.agent.md`:

```markdown
---
name: documentation-writer
description: >
  Creates and improves project documentation, especially Markdown files.
---

You are a technical documentation specialist.

- Explain setup, prerequisites, commands, configuration, and usage.
- Use accurate commands and avoid inventing project features.
- Keep documentation structured and beginner-friendly.
- Prefer examples that can be copied and executed.
- Do not modify application source code.
```

### Delegate a task to a specialist

From your main Copilot session, try:

```text
Use the web-security-auditor agent to inspect the authentication module.
Then use the test-engineer agent to suggest regression tests for any
confirmed issues. Do not modify files. Summarize both results.
```

Copilot decides whether and how to delegate. The exact behavior depends on the task, available agents, model, and CLI version. A custom agent is not a guarantee that multiple agents will always run in parallel.

## 12. A practical live-demo sequence

Use this sequence during the presentation. It demonstrates the workflow progressively rather than simply showing a finished agent.

### Demo 1 — Show the CLI

```bash
copilot --version
copilot
```

Explain that you are now inside the Copilot terminal interface.

### Demo 2 — Ask about the repository

```text
Give me an overview of this project. Identify the main folders and technologies.
```

### Demo 3 — Open the agent menu

```text
/agent
```

Explain the difference between selecting an agent and creating a new one.

### Demo 4 — Create the Web Security Auditor

- Press `N`.
- Choose **Project**.
- Choose **Create with Copilot**.
- Paste the agent description from Section 8.
- Review the generated profile.
- Continue.

### Demo 5 — Show the generated file

Exit Copilot and inspect the file:

```bash
find .github/agents -maxdepth 2 -type f
```

Then print the profile:

```bash
cat .github/agents/web-security-auditor.agent.md
```

The filename may differ if you chose a different agent name.

### Demo 6 — Restart and select the agent

```bash
copilot
```

Then:

```text
/agent
```

Select the Web Security Auditor.

### Demo 7 — Give it a focused task

```text
Review the login module for security weaknesses.
Do not modify files. Provide evidence and remediation.
```

### Demo 8 — Show the manual invocation

Exit the interactive session and run:

```bash
copilot --agent web-security-auditor --prompt "Review the login module. Do not modify files."
```

### Demo 9 — Show Git tracking

```bash
git status
git add .github/agents/
git commit -m "docs: add custom Copilot security agent"
```

If this is a new repository, initialize Git first:

```bash
git init
git add .
git commit -m "chore: initialize Copilot agent demo"
```

To share the agent with collaborators, push the repository to a GitHub remote after configuring that remote.

## 13. Useful commands and shortcuts

### Shell commands used in this walkthrough

| Command                           | What it does                                    |
| --------------------------------- | ----------------------------------------------- |
| `brew --version`                  | Checks Homebrew                                 |
| `brew install --cask copilot-cli` | Installs Copilot CLI with Homebrew              |
| `node --version`                  | Checks Node.js                                  |
| `npm --version`                   | Checks npm                                      |
| `npm install -g @github/copilot`  | Installs Copilot CLI with npm                   |
| `copilot --version`               | Checks the installed CLI version                |
| `copilot help`                    | Displays CLI help                               |
| `mkdir project-name`              | Creates a directory                             |
| `mkdir -p .github/agents`         | Creates the agent directory and missing parents |
| `cd folder-name`                  | Changes the current directory                   |
| `touch file-name`                 | Creates an empty file                           |
| `cat file-name`                   | Prints a file                                   |
| `find path -maxdepth 2 -type f`   | Lists files within a limited directory depth    |
| `git status`                      | Shows repository changes                        |
| `git add`                         | Stages changes                                  |
| `git commit`                      | Creates a commit                                |

### Copilot commands used in this walkthrough

| Command                               | Where to use it | Purpose                                        |
| ------------------------------------- | --------------- | ---------------------------------------------- |
| `copilot`                             | Shell           | Starts interactive Copilot CLI                 |
| `copilot --version`                   | Shell           | Displays version                               |
| `copilot help`                        | Shell           | Displays help                                  |
| `/login`                              | Inside Copilot  | Authenticates                                  |
| `/help`                               | Inside Copilot  | Displays interactive help                      |
| `/agent`                              | Inside Copilot  | Lists/selects agents and starts agent creation |
| `/exit`                               | Inside Copilot  | Exits the interactive session                  |
| `copilot -p "..."`                    | Shell           | Runs a prompt non-interactively                |
| `copilot -sp "..."`                   | Shell           | Runs a prompt and prints only the response     |
| `copilot --agent NAME --prompt "..."` | Shell           | Runs a prompt using a named custom agent       |

Always check `copilot help` and `/help` for the exact options available in your installed version.

## 14. Troubleshooting

### `command not found: copilot`

Check whether the installation completed:

```bash
brew list --cask | grep copilot
```

If you used npm:

```bash
npm list -g --depth=0
```

Try opening a new terminal. If the command is still unavailable, check the installation method and your shell `PATH`.

### `command not found: brew`

Homebrew is not installed or is not on your shell path. Follow the official setup instructions at <https://brew.sh/>.

### Authentication does not work

Inside Copilot CLI, run:

```text
/login
```

Complete the browser/device flow. If your account is managed by an organization, ask the administrator whether Copilot CLI is enabled.

### The new agent does not appear

1. Confirm the file is in `.github/agents/` or `~/.copilot/agents/`.
2. Confirm the file ends in `.agent.md`.
3. Check that the Markdown frontmatter is valid.
4. Restart Copilot CLI.
5. Run `/agent` again.

### The agent is not following the intended role

- Make the `description` specific about when the agent should be used.
- Write clear, actionable instructions.
- Define boundaries and expected output.
- Restrict tools if the agent does not need broad access.
- Test with a small task before using it on a large repository.

## 15. Security and responsible use

An AI agent can inspect files and, depending on its tool permissions and the approvals you grant, execute commands or modify files. Treat agent profiles as instructions—not as a security boundary by themselves.

Follow these practices:

- Start with read-only tasks when demonstrating a new agent.
- Review generated agent profiles before accepting them.
- Grant only the tools required for the role.
- Do not paste API keys, passwords, access tokens, or private credentials into prompts.
- Do not run commands from an unknown repository without inspecting them.
- Do not test systems without explicit authorization.
- Keep security testing inside your own lab or an approved scope.
- Review every proposed code change before committing it.
- Remember that repository files and issue text can contain malicious or misleading instructions. Do not let untrusted content override your security rules.

## 16. Key concepts to explain during the presentation

**What is an agent?**  
A specialist configuration that gives Copilot a defined role, instructions, and optionally a specific tool set.

**What is `/agent`?**  
The interactive command used to browse/select available agents and create custom agents.

**What is an `.agent.md` file?**  
A Markdown agent profile. It contains YAML frontmatter (such as name and description) and the agent's instructions.

**Project agent vs User agent?**  
A project agent lives in the repository and can be shared through Git. A user agent lives in your home directory and is available across your own projects.

**What is a subagent?**  
A specialist agent that the main Copilot session can delegate a focused task to. It has its own context for that delegated work.

**Are custom agents separate AI models?**  
Not necessarily. A custom agent is primarily a role and configuration. Model selection can be configured in supported scenarios, but creating an agent does not automatically create or host a new model.

**Can agents work together?**  
Copilot can delegate suitable subtasks to custom agents. The main agent remains responsible for coordinating the task and combining results. Delegation and parallel execution depend on the task and available capabilities.

**Why save agents in `.github/agents/`?**  
It keeps project-specific agent definitions version-controlled and shareable with the team.

---

## 17. Official references

Use these official pages for the latest behavior and command options:

1. [GitHub Copilot CLI quickstart](https://docs.github.com/en/copilot/get-started/cli-quickstart)
2. [Install GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
3. [Create and use custom agents in Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli)
4. [Copilot CLI configuration directory](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-config-dir-reference)
5. [GitHub Copilot CLI repository](https://github.com/github/copilot-cli)

> GitHub updates Copilot CLI regularly. Some menu labels, shortcuts, built-in agents, and command options may change. Use `/help` and `copilot help` in the installed version, and consult the official documentation before presenting version-specific behavior.

---

## Quick-start cheat sheet

```bash
# Install on macOS
brew install --cask copilot-cli

# Verify
copilot --version

# Open your project
cd path/to/your/repository

# Start Copilot
copilot

# Authenticate inside Copilot
/login

# Open agent menu
/agent

# Create an agent directory manually (optional)
mkdir -p .github/agents

# Create an agent profile manually (optional)
touch .github/agents/web-security-auditor.agent.md

# Run a named agent from the shell
copilot --agent web-security-auditor --prompt "Review the login module. Do not modify files."
```

**End goal:** Install Copilot CLI → authenticate → explore the terminal → create a specialist agent → review its profile → select it with `/agent` → give it a focused task → save and share the agent profile through Git.

# Everyday GitHub Copilot CLI: `@` `/` `#`

Three useful shortcuts make everyday interaction with GitHub Copilot CLI easier. They help you provide context, run commands, and reference information.

> **Note:** The exact suggestions and supported reference types can vary by Copilot CLI version. Use `/help` and the suggestions shown by your installed CLI to confirm availability.

## 1. `@` — Add context

Use `@` to reference files and provide additional context to Copilot.

| Example         | Purpose                                |
| --------------- | -------------------------------------- |
| `@README.md`    | Reference the README file              |
| `@src/app.py`   | Reference a specific source file       |
| `@package.json` | Provide project dependency information |

### Practical examples

```text
Explain @src/app.py
```

```text
Review @src/auth/login.py for security vulnerabilities.
```

```text
Compare @src/old.py and @src/new.py.
```

Think of `@` as saying: **“Copilot, look at this file while answering.”**

## 2. `/` — Run commands

Use `/` to access Copilot CLI's built-in commands and features.

| Command         | Purpose                                      |
| --------------- | -------------------------------------------- |
| `/help`         | Display help                                 |
| `/agent`        | Select or create agents                      |
| `/login`        | Authenticate                                 |
| `/clear`        | Clear conversation context                   |
| `/exit`         | Exit Copilot CLI                             |
| `/model`        | Select an available model                    |
| `/usage`        | View usage information, where supported      |
| `/instructions` | Inspect loaded instructions, where supported |

### Practical examples

```text
/agent
```

```text
/model
```

```text
/help
```

Think of `/` as saying: **“Copilot, perform this CLI action.”**

_Available commands can change with CLI versions. Use `/help` to check your installation._

## 3. `#` — Reference information

Use `#` to reference supported context or information in your Copilot CLI session. Depending on the CLI version and context, available reference types may include:

| Example      | Purpose                                  |
| ------------ | ---------------------------------------- |
| `#file`      | Reference a file                         |
| `#codebase`  | Reference the codebase                   |
| `#selection` | Reference selected code, where supported |

### Practical examples

```text
Explain #codebase and identify the main application entry point.
```

```text
Review #file for possible SQL injection vulnerabilities.
```

_Important: `#` reference types are version- and context-dependent. Check the suggestions shown by your installed CLI rather than assuming every example is supported._

## Quick comparison

| Shortcut | Remember it as | Main purpose                        |
| -------- | -------------- | ----------------------------------- |
| `@`      | Context        | Reference files and content         |
| `/`      | Commands       | Control Copilot CLI                 |
| `#`      | References     | Reference supported code or context |

## Try this everyday example

```text
@src/auth.py
```

```text
Review this file for security vulnerabilities.
```

```text
/agent
```

Select your Security Auditor and ask it to inspect the authentication logic.

## Presentation takeaway

- **`@`** tells Copilot what to look at.
- **`/`** tells Copilot what action to perform.
- **`#`** helps reference supported information and context.

# How to Create `copilot-instructions.md`

`copilot-instructions.md` is a repository-level instruction file that tells GitHub Copilot how to work with your project. It provides shared guidance about the codebase, coding conventions, tools, and project-specific rules.

## 1. Open your project directory

In Terminal, navigate to your Git repository:

```bash
cd path/to/your/project
```

## 2. Create the `.github` directory

```bash
mkdir -p .github
```

**What does this command mean?**

- `mkdir` creates a directory.
- `-p` creates any missing parent directories and does not report an error if the directory already exists.
- `.github` is a hidden directory commonly used for GitHub repository configuration.

## 3. Create the instruction file

```bash
touch .github/copilot-instructions.md
```

This creates an empty Markdown file named `copilot-instructions.md` inside `.github`.

Your project structure should now look like this:

```text
your-project/
├── .github/
│   └── copilot-instructions.md
├── src/
├── README.md
└── ...
```

## 4. Open the file in VS Code

```bash
code .github/copilot-instructions.md
```

If the `code` command is unavailable, open the project in VS Code manually and open `.github/copilot-instructions.md`.

## 5. Add project instructions

Example content:

```markdown
# Project Instructions

## Project Overview

This project is a web application. Follow the existing architecture
and conventions when making changes.

## General Rules

- Inspect the existing code before modifying it.
- Keep changes focused and avoid unnecessary rewrites.
- Explain important changes before implementing them.
- Do not modify unrelated files.
- Do not invent project requirements or functionality.

## Coding Standards

- Use clear, descriptive variable and function names.
- Keep functions small and focused.
- Follow the formatting and naming conventions already used in the project.
- Add comments only when they clarify non-obvious logic.

## Security

- Never hardcode API keys, passwords, or other secrets.
- Validate and safely handle user input.
- Use parameterized database queries.
- Apply authentication and authorization checks where required.
- Do not weaken existing security controls.

## Testing

- Run relevant tests after making changes when authorized.
- Explain which tests were run and whether they passed.
- Do not claim tests passed if they were not executed.
```

Adapt these instructions to your actual project. Avoid adding rules that do not apply.

## 6. Check that Copilot loaded the instructions

Start Copilot CLI from the repository root:

```bash
copilot
```

Inside the interactive session, run:

```text
/instructions
```

This lets you inspect the instruction files Copilot has loaded, where supported by your CLI version.

You can also ask Copilot:

```text
Summarize the project instructions you are following.
```

## 7. Save the file in Git

To share the instructions with collaborators, commit the file to your repository:

```bash
git status
git add .github/copilot-instructions.md
git commit -m "docs: add Copilot repository instructions"
```

After pushing the commit, collaborators who use supported Copilot features in the repository can benefit from the shared guidance.

## Project-level vs personal instructions

| Scope         | Location                                 | Purpose                                                      |
| ------------- | ---------------------------------------- | ------------------------------------------------------------ |
| Repository    | `.github/copilot-instructions.md`        | Instructions shared with the project                         |
| Personal CLI  | `~/.copilot/copilot-instructions.md`     | Personal instructions across projects                        |
| Path-specific | `.github/instructions/*.instructions.md` | Instructions targeted at selected file paths, when supported |

## Quick command summary

```bash
# Navigate to the project
cd path/to/your/project

# Create the GitHub configuration directory
mkdir -p .github

# Create the Copilot instructions file
touch .github/copilot-instructions.md

# Open it in VS Code
code .github/copilot-instructions.md

# Start Copilot CLI
copilot

# Inspect loaded instructions inside Copilot
/instructions

# Commit the instructions
git add .github/copilot-instructions.md
git commit -m "docs: add Copilot repository instructions"
```

## Official documentation

- [GitHub Copilot CLI documentation](https://docs.github.com/en/copilot/how-tos/copilot-cli)
- [Customize GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot)
