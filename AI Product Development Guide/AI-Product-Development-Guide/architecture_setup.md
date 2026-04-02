# architecture_setup.md

# 🏗 Technical Architecture & Setup Guide

> Status: Verified & Ready for Implementation
Version: 1.0.0
Target: Medical/Pharma-Grade ERP
> 

## 1. Core Technology Stack

| Component | Technology | Role | Hosting |
| --- | --- | --- | --- |
| **Frontend** | **Next.js 14** (App Router) | UI, Auth Proxy, AI Streaming | **Vercel** |
| **Styling** | Tailwind CSS + shadcn/ui | Design System | (Build time) |
| **Tables** | AG Grid Enterprise | High-performance Tables | Client-side |
| **Backend** | **Fastify** + Node.js | Business Logic, Event Ledger | **Render** |
| **Database** | **PostgreSQL** (via Supabase) | Persistence, Auth, Storage | **Supabase** |
| **ORM** | Prisma | Schema Management | (Backend) |
| **AI Layer** | LangChain + Claude 3.5 | Agent Reasoning, Schema Gen | **Render** |
| **Analytics** | PostHog | Product Analytics | Cloud |
| **Customer Data** | Snowflake | Large-scale Analytics (Future) | Snowflake |

---

## 2. System Architecture & Data Flow

The system uses a **"Brain, Face, Vault"** architecture to ensure security and compliance while maintaining a fast UX.

### The "Triangle" Flow

1. **The Face (Next.js on Vercel)**
    - Serves the UI.
    - Authenticates the user via Supabase Auth (Cookie).
    - Proxies all API requests to the Backend (hiding the internal API URL).
    - Streams AI responses to the browser.
2. **The Brain (Fastify on Render)**
    - Receives requests from Next.js.
    - **Validates** permissions (RBAC).
    - **Writes** to the **Event Ledger** (Audit Trail).
    - **Executes** the change in the DB (via Prisma).
    - Triggers background automations/agents.
3. **The Vault (Supabase)**
    - Stores the raw data (PostgreSQL).
    - Broadcasts "Realtime" events (e.g., "Row Updated") back to the Browser for multiplayer presence.
    - **Crucial Rule:** The Browser **NEVER** writes to Supabase directly. All writes go through "The Brain" (Fastify).

```mermaid
graph TD
    User[Browser / User]

    subgraph Vercel
        Next[Next.js Frontend]
    end

    subgraph Render
        Fastify[Fastify API (Brain)]
        LangChain[AI Agent Worker]
    end

    subgraph Supabase
        Auth[Auth Service]
        DB[(PostgreSQL DB)]
        Realtime[Realtime PubSub]
    end

    %% Flows
    User -- 1. Login/Read UI --> Next
    User -- 4. Listen for Updates --> Realtime

    Next -- 2. Proxy Request (with Token) --> Fastify

    Fastify -- 3. Write Event/Data --> DB
    Fastify -- Trigger --> LangChain

    LangChain -- Read Schema --> DB
    LangChain -- Propose Change --> Fastify

    DB -- Change Data Capture --> Realtime

```

---

## 3. Repository Structure (Monorepo)

We use a monorepo to keep types synchronized between the Frontend, Backend, and AI Agents.

```
/
├── apps/
│   ├── web/                # Next.js 14 Frontend
│   │   ├── app/            # App Router (Pages & Layouts)
│   │   ├── components/     # UI Components (shadcn/ui)
│   │   └── lib/api.ts      # Typed API Client for Fastify
│   │
│   ├── api/                # Fastify Backend
│   │   ├── src/
│   │   │   ├── plugins/    # Auth, DB, Redis connections
│   │   │   ├── routes/     # API Endpoints (Schema, Tables, Automations)
│   │   │   └── services/   # Business Logic (Ledger, Migrations)
│   │   └── prisma/         # Database Schema & Migrations
│   │
│   └── agents/             # AI Logic (LangChain)
│       └── src/
│           ├── tools/      # "List Tables", "Draft Schema"
│           └── graphs/     # LangGraph workflows
│
├── packages/
│   └── shared/             # Shared TypeScript Code
│       ├── types/          # Domain Models (Table, Field, Tenant)
│       └── events/         # Event Sourcing Payloads
│
└── infra/                  # Dockerfiles, CI/CD, Terraform

```

---

## 4. Key Implementation Guidelines

### A. The "Event Ledger" (Compliance)

Every mutation in the system must produce an **Event Record**.

- **Table:** `event_ledger`
- **Columns:** `id`, `tenant_id`, `actor_id`, `event_type` (e.g., `SCHEMA_UPDATE`, `ROW_EDIT`), `payload` (JSON), `created_at`.
- **Hard Rule:** No direct Prisma `update()` without a corresponding `create()` in the ledger within the same transaction.

### B. AI "Safe Mode"

AI Agents (LangChain) are **read-only** by default.

1. User asks: "Create a CRM for me."
2. Agent generates a JSON **Proposal**.
3. Backend stores Proposal as `status: PENDING`.
4. User sees "Review Changes" in UI.
5. User clicks "Approve" $\to$ Backend executes the changes.

### C. Multiplayer "Optimistic" UI

1. **User A** edits a cell.
2. **AG Grid** updates locally immediately (Optimistic).
3. **Next.js** sends API request to Fastify.
4. **Fastify** writes to DB.
5. **Supabase** emits `postgres_changes` event.
6. **User B's** browser receives event $\to$ Updates their grid.