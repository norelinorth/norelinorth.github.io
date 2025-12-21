---
layout: post
title: "AI That Tracks Its Own Work: MCP Task Management for ERPNext"
date: 2025-12-15
categories: [erpnext, ai, mcp, task-management]
tags: [claude, erpnext, mcp, frappe, kanban, ai-coding, developer-tools]
author: Noreli North
description: "Connect Claude AI to ERPNext's Kanban board via MCP. Watch your AI create projects, update task status, and log affected files - all automatically while it codes."
image: /assets/ai_coding.png
---

**Key Results**:
- ⚡ **Real-time task tracking** (AI updates its own Kanban board)
- ✅ **100% standards compliant** (no core modifications)
- 🤖 **AI accountability** (every file change logged)
- 📊 **Professional audit trail** (full history in ERPNext)
- 🚀 **10-minute setup** (production-ready immediately)


[Video Demo](https://youtu.be/pucBlPziCHI) | [Documentation](https://norelinorth.github.io)

---

![alt text](/assets/ai_coding.png)

## The Problem: AI Without Accountability

AI coding assistants write incredible code. But ask yourself:

**What did your AI actually do today?**

If your answer involves scrolling through chat logs, you have a problem.

Your team uses Jira. Or Asana. Or Monday. Real project management.

Your AI? It lives in a chat window that disappears when you close the tab.

---

## The Solution: MCP Task Management

Using [MCP (Model Context Protocol)](https://modelcontextprotocol.io), I connected Claude directly to ERPNext's task management system.

Now Claude doesn't just write code. **It manages its own work.**

### What Claude Can Do

| MCP Tool | What It Does |
|----------|--------------|
| `create_project` | Create an Agent Project for Kanban tracking |
| `create_task` | Add tasks to the board with type and priority |
| `update_task_status` | Move cards: To Do → In Progress → Testing → Done |
| `complete_task` | Mark done with output summary and affected files |
| `add_task_step` | Log execution steps as work progresses |
| `get_project_tasks` | Retrieve all tasks for status reports |

---

## How It Works

### Step 1: Create a Project

```
"Create a project called 'User Authentication' and break it into tasks"
```

Claude creates `PROJ-0042` in ERPNext and generates logical subtasks.

### Step 2: Watch the Kanban Update

As Claude works:
- Task moves to **"In Progress"** when it starts coding
- **Affected files** are logged as they're modified
- Task moves to **"Done"** with a summary when complete

**The board updates in real-time.** You watch cards move while Claude codes.

### Step 3: Full Audit Trail

Every completed task includes:
- Output summary (what was accomplished)
- Affected files (what was modified)
- Execution steps (how it was done)
- Timestamps (when it happened)

---

## The Power Prompts

### Autonomous Developer Mode

```
Work like a senior developer. Create a project for [feature].
Break it into tasks. Update status as you work. Log affected files.
If blocked, mark blocked and explain why. Start now.
```

### Status Sync

```
I have a standup in 5 minutes. Look at all my Agent Projects
from yesterday and today. What did I complete? What's in progress?
Any blockers? Format it for standup.
```

### Handoff Document

```
Generate a handoff document for project PROJ-0001.
Include all completed tasks, blocked items, affected files,
and recommended next steps. Make it professional.
```

### Accountability Check

```
Look at project PROJ-0001 honestly. Are any tasks marked "Done"
that shouldn't be? Did you cut corners? What would a code
reviewer flag? I need truth, not reassurance.
```

---

## Daily Workflow

| When | What I Do | What Happens |
|------|-----------|--------------|
| **Morning** | "Create today's tasks based on sprint goals" | Tasks appear on board before standup |
| **Working** | "Work on task TASK-00123, update status as you go" | Board shows real-time progress |
| **End of Day** | "Summarize what we accomplished today" | Accurate summary from task data |
| **Standups** | "Generate standup update from yesterday's tasks" | Professional format, 3 seconds |

---

## Setup Guide (10 Minutes)

### 1. Install the MCP App

```bash
cd frappe-bench
bench get-app mcp https://github.com/[your-repo]/mcp
bench --site yoursite.local install-app mcp
```

### 2. Generate API Key

ERPNext → Settings → API Keys → Create new key

### 3. Configure Claude Code

```json
{
  "mcpServers": {
    "erpnext": {
      "command": "python",
      "args": ["-m", "mcp_erpnext"],
      "env": {
        "ERPNEXT_URL": "http://localhost:8000",
        "ERPNEXT_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

### 4. Test Connection

```
"List my installed apps on ERPNext"
```

### 5. First Project

```
"Create a test project called 'MCP Setup Complete'"
```

Check your ERPNext Task Board. Done.

---

## Why This Matters

| Era | AI Capability |
|-----|---------------|
| 2023 | AI writes code |
| 2024 | AI writes code and explains it |
| 2025 | **AI writes code, tracks progress, integrates with systems** |

This is the third phase.

AI isn't just a tool anymore. It's a **collaborator**. And collaborators need:
- Accountability
- Communication
- Integration with existing workflows

MCP makes this possible. ERPNext provides the system. Claude provides the intelligence.

---

## What's Next

Building more MCP integrations:
- **Automatic PR descriptions** from task data
- **Sprint velocity tracking** based on AI work
- **Code review workflows** with task-linked comments
- **Time tracking** correlated with code complexity

What would you want Claude to track?

---

The gap between "AI wrote this code" and "we have professional tracking of AI work" is closing.

MCP is the bridge. ERPNext is the system. Claude is the brain.

Setup takes 10 minutes. The productivity gain? That's up to you.

**Try it. Break it. Tell me what you build.**

---

**Tags**: #ClaudeAI #ERPNext #MCP #TaskManagement #AIProductivity #Kanban #DeveloperTools
