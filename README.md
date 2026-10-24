# 🛡️ OS-Sentinel: AI-Powered Linux OS Tutor & Risk Analyzer

> **Version 3.5** — Intelligent pedagogical platform for Linux operating system concepts, deterministic command risk stratification, safe in-memory VFS sandboxing, and interactive learning.

---

## 📖 Table of Contents
- [Overview](#-overview)
- [Key Features](#-key-features)
  - [1. ⚡ Command Studio & Risk Stratification](#1--command-studio--risk-stratification)
  - [2. 💬 Continuous AI Tutor Chat](#2--continuous-ai-tutor-chat)
  - [3. 🖥️ In-Memory Virtual Sandbox (VFS)](#3--in-memory-virtual-sandbox-vfs)
  - [4. 🎯 Adaptive Contextual Quiz Lab](#4--adaptive-contextual-quiz-lab)
  - [5. 🎨 Cybernetic Dark Glassmorphism UI](#5--cybernetic-dark-glassmorphism-ui)
- [Architecture & Tech Stack](#-architecture--tech-stack)
- [Project Structure](#-project-structure)
- [Installation & Quick Start](#-installation--quick-start)
  - [Prerequisites](#prerequisites)
  - [Step 1: Clone or Navigate to Repository](#step-1-clone-or-navigate-to-repository)
  - [Step 2: Virtual Environment Setup](#step-2-virtual-environment-setup)
  - [Step 3: Install Dependencies](#step-3-install-dependencies)
  - [Step 4: (Optional) Setup Local Ollama LLM](#step-4-optional-setup-local-ollama-llm)
  - [Step 5: Launch the Application](#step-5-launch-the-application)
- [Supported VFS Commands](#-supported-vfs-commands)
- [Running Automated Tests](#-running-automated-tests)
- [Safety & Isolation Guarantee](#-safety--isolation-guarantee)
- [License & Acknowledgments](#-license--acknowledgments)

---

## 🌟 Overview

**OS-Sentinel** is an educational operating systems workbench designed for students, developers, and system administrators. Learning Linux commands and kernel concepts often involves dealing with high-risk operations (such as `rm -rf`, `chmod 777`, `kill -9`, or `dd`) that can disrupt or compromise host systems. 

OS-Sentinel bridges this gap by providing:
- **Zero-Risk In-Memory Emulation:** A completely safe, deterministic POSIX virtual filesystem (VFS) and process simulator isolated from the host OS.
- **Deterministic Risk Engine:** Instant classification and scoring of commands based on system impact, security hazards, and POSIX semantics.
- **RAG-Grounded AI Tutoring:** Continuous multi-turn AI pedagogical support powered locally by Ollama (`qwen3:4b`) or an offline fallback engine, grounded with POSIX documents stored in ChromaDB.
- **Context-Aware Adaptive Quizzes:** Dynamic assessments tailored in real-time to the student's recent commands and conversational topics.

---

## 🚀 Key Features

### 1. ⚡ Command Studio & Risk Stratification
- **Deterministic Risk Analyzer:** Evaluates commands on a 1–10 safety scale across three distinct levels:
  - 🟢 **SAFE (1–3):** Read-only commands (`pwd`, `ls`, `cat`, `head`, `tail`, `stat`).
  - 🟡 **CAUTION (4–7):** Inode/file-state mutations (`mkdir`, `touch`, `cp`, `mv`, `chmod`).
  - 🔴 **HIGH (8–10):** Irreversible, high-privilege, or destructive actions (`rm -rf`, `chmod 777`, `kill -9`, `sudo`, `shutdown`, `reboot`, `dd`).
- **Syscall & Syntax Dissection:** Tokenizes command strings and maps flags, system calls (`sys_chmod`, `sys_unlink`, `sys_reboot`, `sys_kill`), octal masks, and operands into clear educational explanations.
- **Production Alternatives:** Suggests safer production-grade command patterns (e.g., using `chmod 644/755` instead of `777`, sending `SIGTERM` before `SIGKILL`, or testing `rm` with interactive `-i` flags).

### 2. 💬 Continuous AI Tutor Chat
- **Multi-Turn Conversational Memory:** Maintains context across interactions (e.g., seamlessly answers follow-ups like *"What is a safer alternative to that?"* or *"Why is that dangerous?"*).
- **Intent Recognition:** Automatically classifies user prompts into intents such as `concept`, `comparison`, `alternative`, `syntax`, `troubleshoot`, or `direct_command`.
- **RAG Grounding:** Employs ChromaDB with `sentence-transformers/all-MiniLM-L6-v2` embeddings to ground tutor responses in verified Linux systems literature.
- **Graceful Offline Fallback:** If Ollama is offline or the local model is missing, OS-Sentinel automatically engages its built-in pedagogical knowledge engine without crashing.

### 3. 🖥️ In-Memory Virtual Sandbox (VFS)
- **100% In-Memory POSIX Filesystem (`vfs.py`):** Operates on standard Linux hierarchies (`/`, `/bin`, `/etc`, `/home/student`, `/var`, `/tmp`).
- **File Metadata & Permissions:** Simulates real inode allocation, file sizes, timestamps, octal permission masks, and symbolic string representations (`-rwxr-xr-x`, `drwxr-xr-x`).
- **Shell Redirection:** Supports standard output redirection (`>` overwrite, `>>` append).
- **Virtual Process Table:** Simulates process lifecycles (`systemd`, `bash`, `python`, `chrome`, `nginx`), PID lookups, CPU metrics, and process termination with signals (`kill`).
- **Visual Explorer & Timeline:** Live graphical tree views, file inspectors, and chronological mutation history logs.

### 4. 🎯 Adaptive Contextual Quiz Lab
- **Dynamic Topic Detection:** Automatically tracks concepts explored in the chat and sandbox (e.g., permissions, deletion, signals, process management).
- **Interactive Quizzes:** Delivers multiple-choice questions with real-time score tracking, instant validation, and deep kernel-level explanations.
- **Topic Bank:** Covers POSIX permissions, octal masks, sticky bits, inode link counts, signals (`SIGTERM` vs. `SIGKILL`), process table semantics, and filesystem hierarchies.

### 5. 🎨 Cybernetic Dark Glassmorphism UI
- Modern dark cybernetic theme featuring neon glow accents, animated orbital radar graphics, system status beacons, and ultra-high-contrast text selection for readability.

---

## 🏗️ Architecture & Tech Stack

```
                     ┌────────────────────────────────────────┐
                     │          Streamlit Web UI              │
                     │  (Command Studio, Chat, Sandbox, Quiz) │
                     └──────────────────┬─────────────────────┘
                                        │
           ┌────────────────────────────┼───────────────────────────┐
           ▼                            ▼                           ▼
┌─────────────────────┐      ┌─────────────────────┐     ┌─────────────────────┐
│ Deterministic Risk  │      │  Continuous Tutor   │     │ Virtual Filesystem  │
│  & Syntax Analyzer  │      │   Reasoning Engine  │     │     (vfs.py)        │
└─────────────────────┘      └──────────┬──────────┘     └─────────────────────┘
                                        │
                         ┌──────────────┴──────────────┐
                         ▼                             ▼
              ┌─────────────────────┐       ┌─────────────────────┐
              │  ChromaDB Vector DB │       │   Ollama Local LLM  │
              │ (MiniLM Embeddings) │       │     (qwen3:4b)      │
              └─────────────────────┘       └─────────────────────┘
```

| Layer | Technology | Description |
| :--- | :--- | :--- |
| **Framework & UI** | [Streamlit](https://streamlit.io/) | Interactive dashboard with custom Glassmorphism CSS |
| **Local LLM Inference** | [Ollama](https://ollama.com/) | Local model serving (`qwen3:4b` default) |
| **Vector Database** | [ChromaDB](https://www.trychroma.com/) | Vector store for Linux documentation and POSIX knowledge |
| **Embeddings** | [Sentence Transformers](https://sbert.net/) | `all-MiniLM-L6-v2` dense vector model |
| **Virtual Filesystem** | Python (Standard Library) | Pure in-memory deterministic Linux emulator |
| **Language** | Python 3.10+ | Clean, modular codebase |

---

## 📁 Project Structure

```
OS-Sentinel/
├── app.py                         # Main Streamlit application and AI Tutor interface
├── vfs.py                         # Virtual File System & process simulator engine
├── requirements.txt               # Project Python package dependencies
├── run.bat                        # Windows 1-click batch launcher script
├── os_sentinel_chroma/            # Persistent ChromaDB vector database
├── test_conversational_and_quiz.py# Test suite for intent classification, memory, and quiz
├── test_ollama_integration.py     # Test suite for Ollama and RAG integration
├── test_vfs_visualization.py      # Test suite for VFS commands and state mutations
└── README.md                      # Project documentation
```

---

## ⚙️ Installation & Quick Start

### Prerequisites
- **Python 3.10 or higher** installed on your system.
- *(Optional, for Local AI features)* [Ollama](https://ollama.com/) installed and running.

---

### Step 1: Clone or Navigate to Repository
```bash
git clone https://github.com/your-username/OS-Sentinel.git
cd OS-Sentinel
```

---

### Step 2: Virtual Environment Setup

**Windows (PowerShell / Command Prompt):**
```powershell
python -m venv venv
.\venv\Scripts\activate
```

**Linux / macOS:**
```bash
python3 -m venv venv
source venv/bin/activate
```

---

### Step 3: Install Dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

### Step 4: (Optional) Setup Local Ollama LLM
To enable local generative AI responses:
1. Start the Ollama server:
   ```bash
   ollama serve
   ```
2. Pull the default model:
   ```bash
   ollama pull qwen3:4b
   ```
*(Note: If Ollama is not installed or offline, OS-Sentinel will automatically function using its built-in offline educational engine.)*

---

### Step 5: Launch the Application

#### Option A: Using Windows Launcher
Simply double-click `run.bat` or execute in terminal:
```cmd
run.bat
```

#### Option B: Using Streamlit Directly
```bash
streamlit run app.py
```

Once started, open your browser and navigate to:
```
http://localhost:8501
```

---

## 💻 Supported VFS Commands

The in-memory sandbox strictly models Linux file operations, paths, flags, and return codes:

| Command | Flags & Modes | Description |
| :--- | :--- | :--- |
| `pwd` | — | Print working directory |
| `cd` | `~`, `.`, `..`, paths | Change current working directory |
| `ls` | `-l`, `-a`, `-h`, `-la` | List directory contents with permissions and sizes |
| `mkdir`| `-p` / `--parents` | Create new directories |
| `touch`| — | Create empty files or update timestamps |
| `cat` | — | Display file contents |
| `echo` | `>`, `>>` | Output text or redirect to files (overwrite / append) |
| `cp` | `-r` / `-R` | Copy files or directories recursively |
| `mv` | — | Move or rename files and directories |
| `rm` | `-r`, `-f`, `-rf` | Remove files or unmount directories |
| `rmdir`| — | Remove empty directories |
| `chmod`| `-R`, octal (`644`, `755`, `777`) | Modify POSIX mode bits |
| `stat` | — | Inspect detailed inode metadata and permissions |
| `head` | — | View first 10 lines of a file |
| `tail` | — | View last 10 lines of a file |
| `tree` | — | Visual hierarchical directory tree rendering |
| `find` | `-name`, `-type` | Search for files by name or type |
| `ps` | — | List running virtual processes and CPU metrics |
| `kill` | `-9`, `<PID>` | Terminate virtual processes with signals |
| `shutdown`| `now`, `reboot`, `halt` | Safe simulation of system shutdown sequences |

---

## 🧪 Running Automated Tests

OS-Sentinel includes comprehensive test suites covering all major subsystems:

```bash
# 1. Test conversational tutor, intent parsing, and adaptive quizzes
python test_conversational_and_quiz.py

# 2. Test VFS operations, file lifecycle, and state mutation tracking
python test_vfs_visualization.py

# 3. Test Ollama connectivity and ChromaDB RAG retrieval
python test_ollama_integration.py
```

---

## 🔒 Safety & Isolation Guarantee

- **Host Safety:** All file creation, modification, deletion, and process termination commands executed within the Virtual Sandbox operate strictly on an in-memory dictionary data structure. 
- **Zero Host Mutation:** Running dangerous commands like `rm -rf /` or `chmod 777` inside OS-Sentinel will **never** alter, delete, or endanger any files on your actual operating system.

---

## 📄 License & Acknowledgments

- Developed for Linux Systems Education & Operating System Security Learning.
- Powered by open-source technologies: **Streamlit**, **Ollama**, **ChromaDB**, and **Sentence-Transformers**.
