---
title: "How to Set Up Multi-Model AI Orchestration with Claude, Codex, and Gemini"
description: "Step-by-step guide to implementing Burke Holland's ultralight orchestration system for coordinating multiple AI models in VS Code."
pubDate: 2026-02-08
tags: ["AI", "Development", "Tutorial", "VS Code", "Productivity"]
---

## The Problem with Multi-Model Workflows

Most developers choose between AI models: Opus *or* Codex *or* Gemini. But what if you could use all three simultaneously, each doing what it does best?

**Burke Holland** ([@burkeholland](https://x.com/burkeholland)) recently shared an elegant solution: orchestrate multiple AI models like a development team, with each model playing to its strengths.

> "I've got a solution to Opus vs Codex... BOTH. Opus plans and divides up work. Codex implements however it thinks best. Gemini handles all design tasks and wears a cardigan."  
> — [Burke Holland, Feb 7, 2026](https://x.com/burkeholland/status/2019856948309479519)

## The Architecture

Burke's system creates four specialized agents that work together:

1. **Orchestrator (Claude Sonnet 4.5)** — Strategic planner, delegates work
2. **Planner (GPT-5.2)** — Creates detailed implementation plans
3. **Coder (GPT-5.2-Codex)** — Fast, practical code implementation
4. **Designer (Gemini 3 Pro)** — UI/UX specialist

It's like having a senior architect, a project manager, two developers, and a designer on call 24/7.

## Prerequisites

Before you begin, you'll need:

- **VS Code Insiders** (required for chat agent protocol)
- **GitHub Copilot subscription** (for agent orchestration)
- **Access to Claude, GPT-5.2, Codex, and Gemini APIs**

## Step-by-Step Installation

### 1. Install VS Code Insiders

Download from [https://code.visualstudio.com/insiders/](https://code.visualstudio.com/insiders/)

VS Code Insiders is required for the chat agent protocol that enables multi-agent orchestration.

### 2. Install the Four Agents

Burke has packaged each agent as a one-click install. Click each link below **in order**:

#### Agent 1: Orchestrator (Required - Install First)
**Model:** Claude Sonnet 4.5  
**Role:** Receives requests, delegates work, integrates results

[Install Orchestrator Agent](https://aka.ms/awesome-copilot/install/agent?url=vscode-insiders%3Achat-agent%2Finstall%3Furl%3Dhttps%3A%2F%2Fgist.githubusercontent.com%2Fburkeholland%2F0e68481f96e94bbb98134fa6efd00436%2Fraw%2Forchestrator.agent.md)

This agent:
- Analyzes your requests and gathers context
- Delegates planning to the Planner agent
- Delegates code implementation to the Coder agent
- Delegates UI/UX work to the Designer agent
- Integrates all results and validates final output

#### Agent 2: Planner
**Model:** GPT-5.2  
**Role:** Creates comprehensive implementation plans

[Install Planner Agent](https://aka.ms/awesome-copilot/install/agent?url=vscode-insiders%3Achat-agent%2Finstall%3Furl%3Dhttps%3A%2F%2Fgist.githubusercontent.com%2Fburkeholland%2F0e68481f96e94bbb98134fa6efd00436%2Fraw%2Fplanner.agent.md)

This agent:
- Researches your codebase
- Consults documentation
- Identifies edge cases
- Creates detailed implementation plans

#### Agent 3: Coder
**Model:** GPT-5.2-Codex  
**Role:** Fast, practical code implementation

[Install Coder Agent](https://aka.ms/awesome-copilot/install/agent?url=vscode-insiders%3Achat-agent%2Finstall%3Furl%3Dhttps%3A%2F%2Fgist.githubusercontent.com%2Fburkeholland%2F0e68481f96e94bbb98134fa6efd00436%2Fraw%2Fcoder.agent.md)

This agent:
- Writes clean, structured code
- Follows architectural best practices
- Implements proper error handling
- Uses context7 MCP Server for documentation lookup

#### Agent 4: Designer
**Model:** Gemini 3 Pro  
**Role:** UI/UX specialist

[Install Designer Agent](https://aka.ms/awesome-copilot/install/agent?url=vscode-insiders%3Achat-agent%2Finstall%3Furl%3Dhttps%3A%2F%2Fgist.githubusercontent.com%2Fburkeholland%2F0e68481f96e94bbb98134fa6efd00436%2Fraw%2Fdesigner.agent.md)

This agent:
- Focuses on usability and accessibility
- Creates aesthetic interface designs
- Ensures consistent user experience

### 3. Verify Installation

After installing all four agents, open VS Code Insiders and:

1. Open the Command Palette (`Cmd+Shift+P` or `Ctrl+Shift+P`)
2. Type "Chat: Focus on Chat View"
3. You should see the Orchestrator agent available

## How to Use It

### Basic Usage

1. Open VS Code Insiders
2. Open the Chat panel
3. Select the **Orchestrator** agent
4. Send your request

**Example prompts:**
```
@orchestrator Build a React component for a product card with 
image, title, price, and add-to-cart button

@orchestrator Refactor this authentication flow to use JWT 
tokens instead of sessions

@orchestrator Create a dark mode toggle with smooth transitions 
and persistent state
```

### What Happens Behind the Scenes

1. **Orchestrator** receives your request and analyzes it
2. **Planner** creates a detailed implementation plan
3. **Coder** implements the code following the plan
4. **Designer** handles any UI/UX aspects
5. **Orchestrator** integrates everything and presents the final result

You interact with one agent, but four models work together behind the scenes.

## The Coordination Challenge

Burke's key insight: **"The trick is coordinating them without creating chaos."**

His system solves this by:
- **Clear role separation** — Each agent has a specific domain
- **Hierarchical delegation** — Orchestrator manages all communication
- **Single interface** — You only talk to the Orchestrator
- **Validated output** — Orchestrator checks work before delivering

## Why This Works

**Traditional approach:**
- Choose one model
- Accept its strengths and weaknesses
- Manual context switching between tools

**Orchestrated approach:**
- Use all models simultaneously
- Each model does what it does best
- Automatic coordination and integration
- Single coherent output

The result: Higher quality output with less manual coordination overhead.

## Performance Notes

**What to expect:**
- Response time is slightly longer (multiple model calls)
- Higher quality output (specialized expertise per task)
- Reduced back-and-forth (comprehensive first pass)

**Best for:**
- Complex features requiring planning + implementation + design
- Refactoring projects
- New component/module creation

**Not ideal for:**
- Quick one-liners or simple tasks
- Pure debugging (use Coder directly)
- Pure design work (use Designer directly)

## Troubleshooting

### Agents not appearing
- Ensure you're using **VS Code Insiders**, not regular VS Code
- Restart VS Code after installation
- Check GitHub Copilot is properly authenticated

### Orchestrator not delegating
- Make sure all four agents are installed
- Try being more explicit: "Plan this first, then implement"

### Slow responses
- Normal for first request (context building)
- Subsequent requests in same session are faster

## Advanced: Customizing the Agents

Burke's agents are defined in markdown files. To customize:

1. View the gist: [github.com/burkeholland/0e68481f96e94bbb98134fa6efd00436](https://gist.github.com/burkeholland/0e68481f96e94bbb98134fa6efd00436)
2. Fork the gist
3. Modify the agent definitions (e.g., change models, adjust behavior)
4. Install from your fork URL

## Try It Yourself

Burke's system is production-ready and available now. The one-click install links above will have you running in under 5 minutes.

**Original source:**
- Twitter thread: [x.com/burkeholland/status/2019856948309479519](https://x.com/burkeholland/status/2019856948309479519)
- GitHub Gist: [gist.github.com/burkeholland/0e68481f96e94bbb98134fa6efd00436](https://gist.github.com/burkeholland/0e68481f96e94bbb98134fa6efd00436)
- Burke Holland on X: [@burkeholland](https://x.com/burkeholland)

## The Future of AI Development

This orchestration pattern is where the real productivity wins are hiding. Instead of debating which model is "best," we can now use all of them—each contributing its unique strengths to a unified workflow.

Burke calls it "Ultralight Orchestration," and it's the simplest multi-agent system I've seen that actually works in practice.

Give it a shot. The cardigan is optional.

---

*Full credit to **Burke Holland** ([@burkeholland](https://x.com/burkeholland)) for designing and sharing this system. Installation instructions adapted from his [original gist](https://gist.github.com/burkeholland/0e68481f96e94bbb98134fa6efd00436).*
