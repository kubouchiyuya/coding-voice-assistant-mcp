# coding-voice-assistant-mcp

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12,20,30&height=200&section=header&text=coding-voice-assistant-mcp&fontSize=36&fontColor=fff&animation=fadeIn&desc=Voice-Guided%20Full-Cycle%20Coding%20Assistant%20(MCP%20Edition)&descSize=18&descAlignY=70" width="100%"/>

<img src="https://raw.githubusercontent.com/kubouchiyuya/kubouchiyuya/main/avatar.jpg" width="120" style="border-radius:50%; margin-top:16px"/>

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=22&duration=3000&pause=1000&color=F97316&center=true&vCenter=true&multiline=true&width=900&height=140&lines=5+Council+Agents+%F0%9F%A4%96;7+Execution+Agents;Voice-Guided+Development)](https://github.com/DenverCoder1/readme-typing-svg)

[![GitHub](https://img.shields.io/badge/GitHub-kubouchiyuya-181717?style=for-the-badge&logo=github)](https://github.com/kubouchiyuya)
[![LP](https://img.shields.io/badge/Consulting%20LP-F97316?style=for-the-badge&logo=vercel&logoColor=white)](https://consulting-payment-tool.vercel.app)

</div>

---

## What This Does

This repository is the MCP edition of a voice-guided coding assistant. It coordinates AI agents through an MCP interface, then uses voice output to make the workflow easier to follow for non-engineers.

It is built for full-cycle execution: intake, planning, critique, implementation, and reporting.

---

## ✨ Features

| Feature | Description |
|---|---|
| 5 council agents | Architecture, strategy, critique, verification, and coordination |
| 7 execution agents | Discrete workers that perform the implementation tasks |
| Voice guidance | Spoken prompts for each step of the workflow |
| Non-engineer friendly | Plain-language explanations and setup prompts |
| MCP-native | Designed around model-context protocol tool workflows |

---

## 🚀 Quick Start

```bash
cd ~/Projects
git clone https://github.com/kubouchiyuya/coding-voice-assistant-mcp.git
cd coding-voice-assistant-mcp
./scripts/settings.sh
```

---

## 🏗️ Architecture

```mermaid
graph TD
    Lead[Lead / Human Intent] --> Council[5 Council Agents]
    Council --> Architect[Architect]
    Council --> Strategist[Strategist]
    Council --> Critic[Critic]
    Council --> Verifier[Verifier]
    Council --> Coordinator[Coordinator]
    Council --> Executors[7 Execution Agents]
    Executors --> MCP[MCP Tools]
    MCP --> Voice[Voice Output]
    Voice --> Lead
```

---

## 🛠️ Tech Stack

![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)
![MCP](https://img.shields.io/badge/MCP-Model%20Context%20Protocol-blue?style=for-the-badge)
![VOICEVOX](https://img.shields.io/badge/VOICEVOX-Free-brightgreen?style=for-the-badge)

---

## 👤 Author

<div align="center">

| | |
|---|---|
| **Yuya Kubouchi (MASA2 / MASA Sub)** | Beauty Salon Owner → AI Architect |
| Location | Osaka, Japan |
| System | AKATSUKI / Miyabi Society |
| Identity | AI × Beauty Industry × Non-Engineer |

</div>
