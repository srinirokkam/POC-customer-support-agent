# Customer Support Agent

An advanced, fully customizable customer support chat interface powered by Anthropic's Claude, leveraging **Amazon Bedrock Knowledge Bases** for contextual, retrieval-augmented knowledge lookup.

> **Note:** This is a cloud-deployed application (AWS Bedrock + Amplify), not a standalone app you can run with a single click. See [Architecture](#architecture) and [Deployment](#deployment) below for how it's built and hosted.

---

## Table of Contents
- [Overview](#overview)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Key Components](#key-components)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Local Development Setup](#local-development-setup)
- [Environment Variables](#environment-variables)
- [Amazon Bedrock Knowledge Base Setup](#amazon-bedrock-knowledge-base-setup)
- [Deployment](#deployment)
- [Demo](#demo)
- [Roadmap](#roadmap)
- [Acknowledgments](#acknowledgments)

---

## Overview

This project implements an AI-powered customer support agent that combines **Claude's conversational intelligence** with **Amazon Bedrock Knowledge Bases** for grounded, source-backed responses. Rather than relying purely on the model's training data, the agent retrieves relevant context from a connected knowledge base in real time, reducing hallucination and enabling domain-specific support.

It's designed as a reference implementation for building production-style RAG (Retrieval-Augmented Generation) support tools — showing not just "chat with an LLM," but the surrounding infrastructure: retrieval, debug visibility, mood-aware routing, and cloud deployment.

## Key Features

- 🤖 **AI-powered chat** using Anthropic's Claude model
- 📚 **Amazon Bedrock integration** for contextual knowledge retrieval (RAG)
- 🔍 **Real-time thinking & debug information display** — see the model's reasoning and retrieval steps as they happen
- 📄 **Knowledge base source visualization** — shows which documents/sources informed each answer
- 😊 **User mood detection** with automatic redirection to appropriate support flows/agents
- 🎨 **Highly customizable UI** built with shadcn/ui components

## Architecture

```
┌─────────────┐      ┌──────────────┐      ┌───────────────────────┐
│   Frontend   │ ───▶│   API Layer  │ ───▶│   Anthropic Claude    │
│ (shadcn/ui)  │     │ (Next.js/API)│      │        API            │
└─────────────┘      └──────┬───────┘      └───────────────────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Amazon Bedrock   │
                    │ Knowledge Base   │
                    │ (RAG retrieval)  │
                    └──────────────────┘
```

The chat UI sends user queries to the backend, which retrieves relevant context from the Bedrock Knowledge Base and passes it to Claude alongside the conversation. Claude's response — along with retrieved sources and reasoning — is streamed back to the UI.

*(Replace this ASCII diagram with an actual architecture image/diagram once finalized — it reads much stronger visually.)*

## Technology Stack

| Layer               | Technology                              |
|---------------------|---------------------------------------  |
| AI Model            | Anthropic Claude                        |
| Knowledge Retrieval | Amazon Bedrock Knowledge Bases (RAG)    |
| Language            | TypeScript                              |
| Frontend Framework  | Next.js                                 |
| UI Components       | shadcn/ui                               |
| Styling             | Tailwind CSS + PostCSS                  |
| Deployment          | AWS Amplify (`amplify.yml` build config)|

## Key Components

A quick map of how the main features connect to the code:

| Feature                              | Implemented In                            |
|---------------------------------------|------------------------------------------|
| Chat interface & message flow         | `components/ChatArea.tsx`                |
| Knowledge base source visualization   | `components/FullSourceModal.tsx`         |
| Navigation & layout                   | `components/TopNavBar.tsx`, `LeftSidebar.tsx`, `RightSidebar.tsx` |
| Mood detection / category routing     | `app/lib/customer_support_categories.json` |
| Chat API / Claude + Bedrock calls     | `app/api/chat/route.ts`                  |
| Theming                               | `components/theme-provider.tsx`          |

## Project Structure

```
customer-support-agent/
├── app/
│   ├── api/chat/
│   │   └── route.ts              # API route handling chat requests
│   ├── lib/
│   │   ├── customer_support_categories.json  # Category/mood definitions
│   │   └── utils.ts
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ui/                        # shadcn/ui primitives
│   │   ├── avatar.tsx
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── dialog.tsx
│   │   ├── dropdown-menu.tsx
│   │   ├── input.tsx
│   │   └── textarea.tsx
│   ├── ChatArea.tsx
│   ├── FullSourceModal.tsx
│   ├── LeftSidebar.tsx
│   ├── RightSidebar.tsx
│   ├── TopNavBar.tsx
│   └── theme-provider.tsx
├── lib/
│   └── utils.ts
├── public/
│   ├── ant-logo.svg
│   ├── claude-icon.svg
│   ├── next.svg
│   ├── vercel.svg
│   ├── wordmark-dark.svg
│   └── wordmark.svg
├── styles/
│   └── themes.js
├── tutorial/                 # Setup walkthrough screenshots
│   ├── access-keys.png
│   ├── attach.png
│   ├── bedrock.png
│   ├── choose-source.png
│   ├── create-knowledge-base.png
│   ├── create-user.png
│   └── preview.png
├── .eslintrc.json
├── .gitignore
├── amplify.yml               # AWS Amplify build configuration
├── components.json          # shadcn/ui configuration
├── config.ts
├── next.config.mjs           # Next.js configuration
├── postcss.config.mjs        # PostCSS configuration
├── tailwind.config.ts        # Tailwind CSS configuration
├── tsconfig.json             # TypeScript configuration
├── package.json
├── package-lock.json
└── README.md
```

## Prerequisites

Before setting this up, you'll need:
- **Node.js** version `>=18.17.0`
- **npm** (or your preferred package manager)
- **AWS account** with access to Amazon Bedrock and a configured Knowledge Base
- **Anthropic API key**
- Familiarity with **TypeScript** and **Next.js** is helpful for customizing the app

## Local Development Setup

```bash
# Clone the repository
git clone https://github.com/YOUR-USERNAME/customer-support-agent.git
cd customer-support-agent

# Install dependencies
npm install

# Create your environment file (see Environment Variables below)
touch .env.local

# Run the development server
npm run dev
```

The app will be available at `http://localhost:3000` (or your configured port).

**Important:** Local development still requires valid AWS Bedrock credentials and a provisioned Knowledge Base — this is not a fully mocked/offline demo.

## Environment Variables

Create a `.env.local` file with the following:

```
ANTHROPIC_API_KEY=your_anthropic_api_key
AWS_REGION=your_aws_region
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
BEDROCK_KNOWLEDGE_BASE_ID=your_knowledge_base_id
```

*(Update variable names to match what your code actually references.)*

## Deployment

This project is configured for deployment via **AWS Amplify**:

1. Push this repository to your GitHub account.
2. In the AWS Amplify console, choose **New app → Host web app**.
3. Connect your GitHub repository and select the branch to deploy.
4. Amplify will detect and use the included `amplify.yml` build configuration automatically (adjust it if your build steps differ).
5. Add the environment variables listed above in the Amplify console under **Environment variables**.
6. Deploy — Amplify will build and host the app, providing a live URL.

You'll also need your **Amazon Bedrock Knowledge Base** provisioned and populated with source documents beforehand, since the app depends on it for retrieval.

## Amazon Bedrock Knowledge Base Setup

This project includes step-by-step screenshots (in `tutorial/`) walking through the AWS Bedrock setup:

1. **Create an IAM user & access keys** — see `tutorial/create-user.png` and `access-keys.png`
2. **Set up Amazon Bedrock** — see `tutorial/bedrock.png`
3. **Choose a knowledge source** — see `tutorial/choose-source.png`
4. **Attach the source to your knowledge base** — see `tutorial/attach.png`
5. **Create the knowledge base** — see `tutorial/create-knowledge-base.png`
6. **Preview & test retrieval** — see `tutorial/preview.png`

Walk through these in order before configuring your `.env.local` — you'll need the resulting Knowledge Base ID and IAM credentials.

## Demo

See `tutorial/preview.png` for a preview of the knowledge base retrieval in action. *(Consider adding a short screen recording or GIF of the full chat UI here too — since this app requires cloud infrastructure to run, visual proof is the best way for reviewers to see it working without deploying it themselves.)*

## Roadmap

- [ ] Add automated tests
- [ ] Add CI/CD pipeline for Amplify deployments
- [ ] Expand mood-detection categories
- [ ] Add multi-language support

## Acknowledgments

This project is adapted from Anthropic's [claude-quickstarts](https://github.com/anthropics/claude-quickstarts) `customer-support-agent` example. It has been extended with:
- Amazon Bedrock Knowledge Base integration for RAG-based retrieval
- User mood detection and redirection logic
- Additional debug/source visualization in the UI
- AWS Amplify deployment configuration
