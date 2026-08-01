# 🧑‍💻 DevPilot AI

<div align="center">

### 🤖 Intelligent Multi-Agent Software Engineering Assistant

*Build, Review, Test, Document & Debug Software with AI.*

🚀 Powered by **OpenAI Agents SDK** • 🧠 Multi-Agent Architecture • 🔄 Reflection Loops • ⚡ Parallel Execution • 👨‍💻 Human-in-the-Loop

</div>

---

## 📖 Overview

**DevPilot AI** is a multi-agent AI Software Engineering Assistant that automates the complete **Software Development Life Cycle (SDLC)** using the **OpenAI Agents SDK**.

Instead of relying on a single AI assistant, DevPilot AI coordinates multiple specialized AI agents that collaborate to:

- 📋 Analyze software requirements
- 💻 Generate production-ready code
- 🔍 Review implementations
- 🧪 Create & execute tests
- 📝 Write documentation
- 🐞 Investigate bugs
- 🧠 Maintain project memory

The system combines **SDK-native agent handoffs**, **reflection loops**, **parallel execution**, **structured outputs**, and **human approval** for critical actions.

---

# ✨ Features

- 🤖 Multi-Agent AI Architecture
- 🔀 OpenAI Agents SDK Handoffs
- 📋 Automated Requirements Analysis
- 💻 AI Coding Assistant
- 🔍 Intelligent Code Reviews
- 🔁 Reflection & Self-Improvement Loop
- 🧪 Automated Test Generation & Execution
- 📝 Documentation Generation
- 🐞 Bug Investigation & Root Cause Analysis
- ⚡ Parallel Agent Execution
- 🧠 Short-Term + Long-Term Memory
- 💾 Persistent Conversation Sessions
- ✅ Human-in-the-Loop Approval System
- 📊 Structured Pydantic Outputs
- 📝 Comprehensive Logging
- 🛡️ Robust Error Handling

---

# 🏗️ Architecture

DevPilot AI follows a **multi-agent architecture** built on the **OpenAI Agents SDK**.

### 🎯 Entry Point

```
User
   │
   ▼
Triage Agent
```

The Triage Agent intelligently routes requests to the most appropriate specialist agent.

### 🤖 Specialist Agents

- 📋 Requirements Analysis Agent
- 💻 Coding Assistant Agent
- 🔍 Code Reviewer Agent
- 🧪 Testing Agent
- 📝 Documentation Writer Agent
- 🐞 Bug Investigation Agent

For complete SDLC automation, these agents are orchestrated in an asynchronous pipeline featuring:

- 🔄 Reflection Loop (Coding ⇄ Review)
- ⚡ Parallel Execution (Testing + Documentation)
- 🌿 Conditional Branching
- 🧠 Shared Context & Memory

---

# 🧰 Built-in Tools

| Tool | Purpose |
|-------|---------|
| 📖 `read_code_file` | Read source files |
| ✍️ `write_code_file` | Write generated code *(approval required)* |
| 🧹 `lint_python_code` | Syntax validation |
| 🧪 `run_python_tests` | Execute pytest suite *(approval required)* |
| 🌐 `search_github_issues` | Search related GitHub issues |
| 📝 `save_documentation` | Save generated documentation *(approval required)* |
| 📊 `save_structured_report` | Export structured JSON reports |

---

# 🧠 AI Agents

## 📋 Requirements Analysis Agent

- Understands feature requests
- Generates implementation plans
- Produces acceptance criteria
- Searches GitHub for related issues

---

## 💻 Coding Assistant Agent

- Generates Python implementations
- Writes production-ready code
- Uses linting before submission

---

## 🔍 Code Reviewer Agent

- Reviews correctness
- Checks style & security
- Approves or requests improvements

---

## 🧪 Testing Agent

- Generates pytest suites
- Runs tests safely
- Produces structured reports

---

## 📝 Documentation Writer Agent

- Creates Markdown documentation
- Generates installation & usage guides

---

## 🐞 Bug Investigation Agent

- Investigates failures
- Finds probable root causes
- Suggests fixes
- Searches similar GitHub issues

---

# 🔄 Workflow

```text
Requirement
      │
      ▼
Requirements Analysis
      │
      ▼
Coding Assistant
      ▲
      │
Code Reviewer
      │
      ▼
──────── Reflection Loop ────────

      ▼
Testing ─────────────┐
                     │
Documentation ───────┘
      │
      ▼
Bug Investigation (if needed)
```

---

# ⚡ Advanced Capabilities

- 🔁 Reflection-based code improvement
- ⚡ Parallel async execution
- 🧠 Persistent project memory
- 💾 SQLite conversation history
- 📊 Typed Pydantic outputs
- 🔀 SDK-native agent handoffs
- 👨‍💻 Human approval workflow
- 📁 Structured report generation
- 📝 Centralized logging
- 🛡️ Exception handling throughout the pipeline

---

# 📂 Project Structure

```text
DevPilot-AI/
│
├── Devpilot AI.ipynb          # Main OpenAI Agents SDK implementation
├── Devpilot AI.pdf            # Project presentation (PDF)
├── Devpilot AI.pptx           # Project presentation (PowerPoint)
├── LICENSE                    # MIT License
├── README.md                  # Project overview and setup guide
├── architecture.png           # Multi-agent architecture diagram
└── documentation.md           # Additional project documentation
```

---

# 🚀 Getting Started

### 1️⃣ Clone the repository

```bash
git clone https://github.com/yourusername/DevPilot-AI.git
cd DevPilot-AI
```

### 2️⃣ Install dependencies

```bash
pip install -r requirements.txt
```

### 3️⃣ Add your API Key

Create an environment variable:

```text
OPENAI_API_KEY=your_api_key
```

or add it to **Google Colab Secrets**.

### 4️⃣ Run the notebook

Open

```
Devpilot AI.ipynb
```

and execute all cells from top to bottom.

---

# 📚 Tech Stack

- 🐍 Python
- 🤖 OpenAI Agents SDK
- 📦 Pydantic
- ⚡ AsyncIO
- 🧪 PyTest
- 📝 Markdown
- 💾 SQLite
- 🌐 GitHub REST API

---

# 🎯 Future Improvements

- 🌍 Multi-language code generation
- 🐳 Docker support
- ☁️ Cloud deployment
- 🖥️ Web dashboard
- 🔗 GitHub Pull Request automation
- 📈 Project analytics
- 🔔 Notifications
- 🔌 Plugin architecture

---

# 📜 License

This project is licensed under the **MIT License**.

---

<div align="center">

### ⭐ If you found DevPilot AI useful, consider giving this repository a star!

**Made with ❤️ using the OpenAI Agents SDK**

</div>