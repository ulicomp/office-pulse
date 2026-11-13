<h1 align="center">Office Pulse</h1>

<p align="center">
  <b>Commission reconciliation and financial health platform for insurance agencies.</b><br>
  Live in production with 2 paying agencies · 12,000+ transactions processed
</p>

<p align="center">
  <img alt="React" src="https://img.shields.io/badge/React-20232A?logo=react&logoColor=61DAFB">
  <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=white">
  <img alt="Tailwind CSS" src="https://img.shields.io/badge/Tailwind_CSS-0F172A?logo=tailwindcss&logoColor=38BDF8">
  <img alt="Supabase" src="https://img.shields.io/badge/Supabase-1C1C1C?logo=supabase&logoColor=3ECF8E">
  <img alt="PostgreSQL" src="https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white">
  <img alt="React Query" src="https://img.shields.io/badge/React_Query-FF4154?logo=reactquery&logoColor=white">
  <img alt="Plaid" src="https://img.shields.io/badge/Plaid-000000?logo=plaid&logoColor=white">
</p>

> **This is a case study repository.** Office Pulse is a commercial product with real agency data, so the source code stays private. This page shows what it does, how it is built, and the engineering problems behind it.

<p align="center">
  <img src="dashboard.png" alt="Office Pulse dashboard: revenue, pending validation, expenses, remaining money, pending commissions, debt and goals" width="900">
</p>

---

## The problem

Insurance agencies get paid by dozens of carriers, each sending commission statements in its own format. Payments arrive partial, late, split across policies, or get clawed back as chargebacks months later. Most agency owners reconcile this by hand in Excel, so revenue leaks quietly and the leak grows as the agency scales.

I lived this problem firsthand working inside an insurance agency, building producer reports and cross-checking exports by hand. Office Pulse is the tool I wished we had.

## What it does

- **One-click commission reconciliation:** matches thousands of carrier payment lines to the exact policy each payment belongs to.
- **Flags what humans miss:** partial payments, completed payments and chargebacks, tracked against what the agency expected to be paid.
- **Policy lifecycle tracking:** commission status for every policy, from written to paid to charged back.
- **Agency financial health:** revenue, expenses, debt and money allocation in one dashboard, with KPIs and reports.
- **Goals and targets:** monthly revenue, production and premium goals, with suggested targets for next month.
- **Bank data through Plaid:** agency bank accounts connect directly, so deposits can be checked against expected commissions.
- **Replaces 40+ hours per month** of manual commission review for an agency.

## Screenshots

**Commission Check:** every carrier payment line matched to its policy, with expected vs. paid, chargebacks, partial payments and lines that need review.

<img src="commission-check.png" alt="Commission Check results" width="900">

| Policy registry | Money by carrier |
|---|---|
| <img src="policies.png" alt="Policy registry with payment status" width="440"> | <img src="carriers.png" alt="Revenue by carrier" width="440"> |

| Revenue | Expenses |
|---|---|
| <img src="revenue.png" alt="Confirmed revenue" width="440"> | <img src="expenses.png" alt="Expense control" width="440"> |

| Goals and next-month targets | Landing page |
|---|---|
| <img src="goals.png" alt="Revenue, production and premium goals" width="440"> | <img src="landing.png" alt="Office Pulse landing page" width="440"> |

<sub>The product UI is in Spanish. Policy numbers and client names are blurred.</sub>

## Architecture

```mermaid
flowchart LR
    subgraph Intake
        A[Carrier statements] --> B[Browser automation<br/>and file intake]
        P[Agency bank accounts] --> Q[Plaid API]
    end

    subgraph Supabase
        B --> E[Edge Functions<br/>parsing and normalization]
        Q --> E
        E --> DB[(PostgreSQL<br/>policies, payments, ledger)]
        DB --> M[Matching and<br/>reconciliation logic]
        M --> DB
    end

    subgraph Frontend
        DB --> RQ[React Query cache]
        RQ --> UI[React + TypeScript + Tailwind<br/>dashboard and reconciliation views]
    end
```

## Engineering highlights

**Reconciling thousands of lines in one click.** The core challenge is allocating every payment line to the policy it actually paid, even when carriers split, merge or partially pay. When a split policy ID was double-counting revenue, I traced it to the root cause and reworked the ledger model instead of patching the symptom.

**Making ledger changes transactional.** Reconciliation actions originally ran as separate browser-side requests. I am moving those mutations behind a single controlled PostgreSQL operation, so a reconciliation either fully applies or does not apply at all. The performance goal: batch verification and matching in under a minute, and individual reconciliation actions that feel instant.

**Designed for zero training.** Agency owners are not accountants or engineers. Every screen is built so the answer is one click away: you know where to click, and you know what you get from each click.

**AI-assisted engineering workflow.** I use Claude Code and Codex daily. I own the architecture and decisions, then work in small audited passes: audit, implement, re-audit, test manually, check performance, ship. The codebase is audited weekly against real user feedback.

## Tech stack

| Layer | Tools |
|---|---|
| Frontend | React, TypeScript, Tailwind CSS, React Query |
| Backend | Supabase (PostgreSQL, Edge Functions) |
| Integrations | Plaid API, browser automation for data intake |
| Workflow | Git/GitHub, Claude Code, Codex |

## Status

Live in production since July 2026, used by 2 paying insurance agencies. Built and maintained solo by me.

---

<p align="center">
  Built by <a href="https://www.linkedin.com/in/ulises-a-cheong-borges-1b06a540a/">Ulises A. Cheong Borges</a> · Miami, FL
</p>
