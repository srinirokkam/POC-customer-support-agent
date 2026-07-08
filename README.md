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
- [Troubleshooting](#troubleshooting)
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
│   Frontend   │ ───▶ │   API Layer  │ ───▶ │   Anthropic Claude     │
│ (shadcn/ui)  │      │ (Next.js/API)│      │        API             │
└─────────────┘      └──────┬───────┘      └───────────────────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Amazon Bedrock    │
                    │ Knowledge Base    │
                    │ (RAG retrieval)   │
                    └──────────────────┘
```

The chat UI sends user queries to the backend, which retrieves relevant context from the Bedrock Knowledge Base and passes it to Claude alongside the conversation. Claude's response — along with retrieved sources and reasoning — is streamed back to the UI.

*(Replace this ASCII diagram with an actual architecture image/diagram once finalized — it reads much stronger visually.)*

## Technology Stack

| Layer               | Technology                          |
|---------------------|---------------------------------------|
| AI Model            | Anthropic Claude                      |
| Knowledge Retrieval | Amazon Bedrock Knowledge Bases (RAG)  |
| Language            | TypeScript                            |
| Frontend Framework  | Next.js                               |
| UI Components       | shadcn/ui                             |
| Styling             | Tailwind CSS + PostCSS                |
| Deployment          | AWS Amplify (`amplify.yml` build config) |

## Key Components

A quick map of how the main features connect to the code:

| Feature                              | Implemented In                          |
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
- **cross-env** (dev dependency, used to set environment variables in the `dev`/`build` scripts consistently across Windows, Mac, and Linux — see Local Development Setup below)
- Familiarity with **TypeScript** and **Next.js** is helpful for customizing the app

## Local Development Setup

```bash
# Clone the repository
git clone https://github.com/YOUR-USERNAME/customer-support-agent.git
cd customer-support-agent

# Install dependencies
npm install

# Install cross-env (required on Windows to run the dev/build scripts correctly)
npm install --save-dev cross-env

# Create your environment file (see Environment Variables below)
touch .env.local

# Run the development server
npm run dev
```

**Note on Windows:** the `dev` and `build` scripts in `package.json` set environment variables inline (e.g. `NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR=true`). This syntax works natively on Mac/Linux, but Windows runs npm scripts through `cmd.exe`, which doesn't support it — resulting in an error like `'NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR' is not recognized as an internal or external command`. Installing `cross-env` and prefixing those scripts with it (e.g. `cross-env NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR=true ... next dev`) resolves this across all operating systems.

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

## Troubleshooting

This section documents real issues encountered while setting this project up and deploying it, in the order they're likely to surface. If the chat responds with something like *"I don't have that information, let me connect you with a human agent"* for basic questions that should be answered by your knowledge base, it usually means retrieval is silently failing — work through the checks below.

### 1. Placeholder Knowledge Base ID never replaced

**Symptom:**
```
RAG Error: ValidationException: 1 validation error detected: Value 'your-knowledge-base-id'
at 'knowledgeBaseId' failed to satisfy constraint...
```

**Cause:** `components/ChatArea.tsx` originally ships with a hardcoded placeholder:
```typescript
const knowledgeBases: KnowledgeBase[] = [
  { id: "your-knowledge-base-id", name: "Your KB Name" },
];
```
This value gets sent from the frontend to the API on every request, so simply changing an environment variable in Amplify has no effect until the code itself is fixed.

**Fix:** Don't trust the frontend for this — resolve the Knowledge Base ID **server-side** in `app/api/chat/route.ts` instead:
```typescript
const { messages, model, knowledgeBaseId } = await req.json();
const resolvedKnowledgeBaseId = process.env.BEDROCK_KNOWLEDGE_BASE_ID;
// ...
const result = await retrieveContext(latestMessage, resolvedKnowledgeBaseId!);
```
This also avoids ever needing to hardcode a real Knowledge Base ID in client-side code (which would otherwise ship inside the public JS bundle).

### 2. Environment variables not available at runtime on Amplify

**Symptom:** Logs show `knowledgeBaseId is not provided` even though the variable is correctly set in the Amplify console.

**Cause:** AWS Amplify injects environment variables at **build time**, not automatically at **runtime** for server-side/SSR code (like Next.js API routes). A variable visible in the console isn't automatically visible to `process.env` inside a deployed API route.

**Fix:** Explicitly write the variables into `.env.production` during the build step, **before** `npm run build` runs, in `amplify.yml`:
```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci --cache .npm --prefer-offline
    build:
      commands:
        - echo "ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY" >> .env.production
        - echo "BEDROCK_KNOWLEDGE_BASE_ID=$BEDROCK_KNOWLEDGE_BASE_ID" >> .env.production
        - echo "BAWS_ACCESS_KEY_ID=$BAWS_ACCESS_KEY_ID" >> .env.production
        - echo "BAWS_SECRET_ACCESS_KEY=$BAWS_SECRET_ACCESS_KEY" >> .env.production
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - "**/*"
  cache:
    paths:
      - .next/cache/**/*
      - .npm/**/*
```
Two easy mistakes to double check here:
- The **variable name on the right side** of each `echo` line must exactly match the name set in the Amplify console (e.g. `$BEDROCK_KNOWLEDGE_BASE_ID`, not a shortened `$KNOWLEDGE_BASE_ID`).
- The `echo` lines must run **before** `npm run build`, not after — otherwise the values aren't available during the build that generates the server bundle.

### 3. Silent failures instead of real errors

**Symptom:** The chat always responds with a generic redirect-to-human message, with no visible error to the user.

**Cause:** `retrieveContext()` is wrapped in a try/catch that fails "quietly" — logging `RAG Retrieval failed but did not throw!` to the server console and returning an empty context, rather than surfacing the real error. The app then interprets the resulting empty context as a trigger for its mood/category redirect logic, masking the actual root cause.

**Fix:** Don't rely on the chat UI to diagnose problems — always check the **Amplify runtime/monitoring logs** for the actual underlying error (`RAG Error: ...`) rather than trusting the user-facing message.

### 4. Managed Knowledge Base vs. Custom Knowledge Base configuration

**Symptom:**
```
RAG Error: ValidationException: Incompatible configuration: vectorSearchConfiguration
is not supported for managed knowledge bases. Use managedSearchConfiguration instead.
```

**Cause:** AWS Bedrock offers two Knowledge Base types — **Managed** (Bedrock handles the vector store for you) and **Customer-managed** (you provide your own vector store, e.g. OpenSearch). The original code was written against the older `vectorSearchConfiguration` field, which only applies to customer-managed knowledge bases. If you created a Managed Knowledge Base through the console (check the Knowledge Base overview page — it will say "Managed" under Embeddings model), you need the other field.

**Fix:** In `app/lib/utils.ts`, inside `retrieveContext()`:
```typescript
// Before
retrievalConfiguration: {
  vectorSearchConfiguration: { numberOfResults: n },
},

// After
retrievalConfiguration: {
  managedSearchConfiguration: { numberOfResults: n },
},
```

### 5. TypeScript build failure after fixing #4

**Symptom:**
```
Type error: Object literal may only specify known properties, and
'managedSearchConfiguration' does not exist in type 'KnowledgeBaseRetrievalConfiguration'.
```

**Cause:** `managedSearchConfiguration` is a newer field, and the installed version of `@aws-sdk/client-bedrock-agent-runtime` predates it — its TypeScript types don't recognize the property yet, even though the field is valid at the API level.

**Fix:** Update the SDK package:
```bash
npm install @aws-sdk/client-bedrock-agent-runtime@latest
git add package.json package-lock.json
git commit -m "Update AWS SDK to support managed knowledge bases"
git push
```

### 6. Windows: "is not recognized as an internal or external command" when running `npm run dev`

**Symptom:**
```
'NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR' is not recognized as an internal or external command,
operable program or batch file.
```

**Cause:** The `dev`/`build` scripts in `package.json` set environment variables inline before the command (e.g. `NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR=true next dev`). This works on Mac/Linux shells, but npm on Windows always executes scripts via `cmd.exe`, which doesn't understand this syntax — regardless of whether you're using Git Bash to type the command.

**Fix:** Install `cross-env` and prefix the relevant scripts with it, so the same script works identically across operating systems:
```bash
npm install --save-dev cross-env
```
```json
"dev": "cross-env NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR=true NEXT_PUBLIC_INCLUDE_RIGHT_SIDEBAR=true next dev"
```

### General debugging tips
- **Add temporary debug logs** liberally around credential/config values you suspect are missing, e.g.:
  ```typescript
  console.log("Have Knowledge Base ID?", !!resolvedKnowledgeBaseId);
  ```
  Remove these once the issue is confirmed fixed.
- **Check build logs, not just runtime logs**, when a deployment fails outright (Amplify Console → your deployment → Build phase) — build failures (like missing/invalid types) show up there, not in the app's runtime logs.
- **Watch out for stray characters in comments** — a `\\` instead of `//` in a `.ts`/`.tsx` file is invalid syntax and will fail the build with a cryptic-looking error pointing at an unrelated line.
- When copying AWS resource IDs (Knowledge Base ID, ARNs, etc.), copy directly from the console rather than retyping — a single dropped character will fail AWS's regex validation with a `ValidationException`.

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
