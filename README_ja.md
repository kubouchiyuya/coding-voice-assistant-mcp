# coding-voice-assistant-mcp

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12,20,30&height=200&section=header&text=coding-voice-assistant-mcp&fontSize=36&fontColor=fff&animation=fadeIn&desc=音声で導く%20フルサイクル%20コーディングアシスタント%20(MCP版)&descSize=18&descAlignY=70" width="100%"/>

<img src="https://raw.githubusercontent.com/kubouchiyuya/kubouchiyuya/main/avatar.jpg" width="120" style="border-radius:50%; margin-top:16px"/>

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=22&duration=3000&pause=1000&color=F97316&center=true&vCenter=true&multiline=true&width=900&height=140&lines=5+つの合議体+%F0%9F%A4%96;7+つの実行体;音声で導く開発)](https://github.com/DenverCoder1/readme-typing-svg)

[![GitHub](https://img.shields.io/badge/GitHub-kubouchiyuya-181717?style=for-the-badge&logo=github)](https://github.com/kubouchiyuya)
[![LP](https://img.shields.io/badge/Consulting%20LP-F97316?style=for-the-badge&logo=vercel&logoColor=white)](https://consulting-payment-tool.vercel.app)

</div>

---

## このツールでできること

このリポジトリは、音声付きのコーディング支援を MCP でまとめたものです。AI エージェントを合議制で動かし、最後に音声で結果を返すので、非エンジニアでも流れを追いやすい構成です。

要件の確認、設計、批評、実装、報告までをひとつの流れとして扱います。

---

## ✨ 機能

| 機能 | 説明 |
|---|---|
| 5 つの合議体 | アーキテクト、戦略、批評、検証、調整 |
| 7 つの実行体 | 実装を担当する個別ワーカー |
| 音声ガイド | 各ステップの進行を音声で案内 |
| 非エンジニア向け | 平易な説明とセットアップ導線 |
| MCP ネイティブ | Model Context Protocol を前提に設計 |

---

## 🚀 はじめかた

```bash
cd ~/Projects
git clone https://github.com/kubouchiyuya/coding-voice-assistant-mcp.git
cd coding-voice-assistant-mcp
./scripts/settings.sh
```

---

## 🏗️ アーキテクチャ

```mermaid
graph TD
    Lead[人間の意図] --> Council[5つの合議体]
    Council --> Architect[アーキテクト]
    Council --> Strategist[戦略担当]
    Council --> Critic[批評担当]
    Council --> Verifier[検証担当]
    Council --> Coordinator[調整担当]
    Council --> Executors[7つの実行体]
    Executors --> MCP[MCP ツール]
    MCP --> Voice[音声出力]
    Voice --> Lead
```

---

## 🛠️ 技術スタック

![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)
![MCP](https://img.shields.io/badge/MCP-Model%20Context%20Protocol-blue?style=for-the-badge)
![VOICEVOX](https://img.shields.io/badge/VOICEVOX-Free-brightgreen?style=for-the-badge)

---

## 👤 作者

<div align="center">

| | |
|---|---|
| **窪内裕也（MASA2 / MASA サブ）** | 美容室オーナー → AI アーキテクト |
| 拠点 | 大阪 |
| 所属 | AKATSUKI / Miyabi Society |
| アイデンティティ | AI × 美容業界 × 非エンジニア |

</div>
