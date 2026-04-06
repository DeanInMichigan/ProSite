---
title: "Chapter 2: Building an Enterprise AI Development Workflow"
description: "What a complete, enterprise-grade software development workflow looks like when AI tools are part of the picture — from requirements to production."
tag: "AI & Development"
order: 5
---

# Chapter 2: Building an Enterprise AI Development Workflow

Last week, we covered the [ten rules every enterprise should have in place before deploying AI coding tools]({{ '/articles/enterprise-ai-dev-rules/' | relative_url }}). Rules matter — but rules without a workflow are just a policy document nobody reads.

This week, we go one level deeper. Here's what a complete, enterprise-grade software development workflow looks like when AI tools are part of the picture — from the first line of requirements to a passing build in production.

We'll focus specifically on two tools that are becoming the de facto standard in enterprise environments: **GitHub Copilot** for inline, IDE-level assistance, and **Claude** (Anthropic) for higher-order tasks like architecture, code explanation, and documentation.

---

## Stage 1: Planning & Requirements

**What it looks like:** Before a single line of code is written, the team is working with user stories, acceptance criteria, and technical specs.

**Where AI fits:** Claude is genuinely useful here. Engineers and product managers can use it to break down vague requirements into structured user stories, identify edge cases that weren't considered, and draft initial technical specs for review. It's a thinking partner — fast, tireless, and surprisingly good at asking the right clarifying questions.

**The rules for this stage:**
- AI-drafted requirements are a starting point, not a deliverable. A human owns the final spec.
- Don't paste customer data, PII, or proprietary business logic into AI prompts. Work at the conceptual level.
- Use AI to pressure-test your requirements: *"What are the failure modes of this approach?"* is a great prompt before you commit to an architecture.

---

## Stage 2: AI-Assisted Development

**What it looks like:** Developers are writing code in their IDE with GitHub Copilot active, and using Claude in a separate window for more complex tasks — refactoring suggestions, explaining unfamiliar codebases, generating boilerplate, or working through architecture decisions.

**Where AI fits:** Copilot handles the high-frequency, low-level completions — filling in function bodies, suggesting parameter names, completing repetitive patterns. Claude handles the longer-context work — reviewing a 200-line function for logic issues, explaining why a particular design pattern applies here, or drafting a migration script with detailed comments.

**The rules for this stage:**
- No credentials, API keys, or secrets in any prompt. Ever. (See Chapter 1, Rule 3.)
- Understand what Copilot suggests before you accept it. Tab-to-accept is a habit that will eventually ship a bug.
- When using Claude for architecture input, treat the output as a peer's suggestion — worth considering, worth pushing back on, not gospel.
- Keep prompts focused. Vague questions get vague answers. *"Write me a REST API"* will produce something; *"Write a POST endpoint in Express for creating a user, with input validation using Zod and error handling that returns RFC 7807 problem details"* will produce something useful.

---

## Stage 3: Local Quality Gates (Before the Commit)

**What it looks like:** Before code ever leaves the developer's machine, automated checks catch the obvious problems.

**Where AI fits:** This is less about AI generating output and more about AI-generated code being held to the same standard as human-written code — which means the same pre-commit hooks apply.

**The rules for this stage:**
- **Secrets scanning** runs on every commit. Tools like GitGuardian, Gitleaks, or GitHub's built-in secret scanning catch credentials before they're in version history forever.
- **Linting and formatting** runs automatically. AI tools sometimes produce stylistically inconsistent code; your linter doesn't care who wrote it.
- **Static analysis** (SonarQube, Semgrep, or similar) should flag common vulnerability patterns. AI-generated code fails these checks at a surprisingly high rate.
- Developers should document AI-assisted commits — a simple tag like `[AI-assisted]` in the commit message creates an audit trail without slowing anyone down.

---

## Stage 4: Source Code Repository

**What it looks like:** Code is pushed to a branch in GitHub, GitLab, or similar, and a pull request is opened against the main branch.

**Where AI fits:** Claude is excellent at writing PR descriptions. A developer can paste their diff and ask Claude to produce a clear, structured summary of what changed and why — which dramatically improves review quality because reviewers actually know what they're looking at.

**The rules for this stage:**
- Branch protection rules are non-negotiable. Nobody merges directly to main, regardless of seniority — and regardless of whether the code was AI-generated.
- PR descriptions should note AI involvement. This isn't about blame; it's about helping reviewers calibrate their scrutiny.
- Require a minimum number of approvals. One is a floor, not a suggestion.
- Copilot-generated code that was accepted without modification deserves a call-out in the PR. It's not that it's necessarily worse — it's that reviewers should know to apply independent judgment rather than deference.

---

## Stage 5: Code Review

**What it looks like:** One or more engineers review the PR. They read the code, run it locally if needed, leave comments, and either approve or request changes.

**Where AI fits:** GitHub Copilot's code review features, and tools like CodeRabbit that integrate with your PR workflow, can provide a first-pass automated review — flagging potential bugs, suggesting improvements, and summarizing the diff for reviewers. This isn't a replacement for human review; it's a pre-flight check that catches the low-hanging fruit so your senior engineers can focus on the judgment calls.

**The rules for this stage:**
- AI-assisted code review is a supplement, not a substitute. A human still approves every merge.
- Reviewers must understand the code, not just agree with it. *"The AI checked it"* and *"I reviewed it"* are not the same thing.
- Pay particular attention to security-sensitive areas: authentication, authorization, data handling, external API calls. This is where AI-generated code most often gets it wrong in subtle ways.
- If a reviewer can't explain what a block of code does, that's a change request — not an approval.

---

## Stage 6: Automated Testing in CI/CD

**What it looks like:** The moment code is pushed, your CI/CD pipeline kicks off. Tests run. Security scans run. If something fails, the build fails and nothing merges.

**Where AI fits:** Claude can generate unit tests from function signatures and docstrings — useful for getting to coverage quickly. But those tests need human review too; an AI that writes both the code and the tests can produce a pair that pass each other perfectly while both being wrong.

**The rules for this stage:**
- **Unit tests:** Minimum coverage thresholds apply to AI-generated code, the same as any other code. There's no exception for "the AI already thought it through."
- **Integration tests:** Automated integration tests run against a staging environment before any merge to main.
- **SAST (Static Application Security Testing):** Snyk, Checkmarx, or GitHub Advanced Security scan for known vulnerability patterns. This catches a significant portion of the security weaknesses commonly introduced by AI-generated code.
- **Dependency scanning:** AI tools love to suggest importing libraries. Every new dependency gets scanned for known CVEs before it ships.
- **Build gates are hard gates.** If tests fail, the PR doesn't merge. No overrides, no "I'll fix it in a follow-up."

---

## Putting It Together: The Full Picture

Here's the workflow end-to-end:

```
Requirements (Claude)
  → Development (Copilot + Claude)
    → Local gates (secrets scan, lint, static analysis)
      → Commit with audit tag
        → PR with AI-assisted description
          → Code review (human + AI pre-check)
            → CI/CD pipeline (tests, SAST, dependency scan)
              → Merge to main
```

Every stage has a human checkpoint. Every stage has automated enforcement. The AI tools accelerate the work at each step — but they don't own any step.

---

## What This Workflow Is Really About

This isn't about making developers slower or less trusted. It's about building the kind of systematic rigor that lets you move fast *and* catch problems before they become incidents.

The teams that will win with AI aren't the ones who give their developers access to the best tools. They're the ones who build workflows that make those tools safe to use at scale — where quality, security, and accountability are baked in, not bolted on.

[Chapter 3: The Baseline Rules for AI-Assisted Development]({{ '/articles/enterprise-ai-baseline-rules/' | relative_url }}) — twenty practical rules for developers and teams, covering prompting, code review, security, and quality gates.

---

*How does your team's current workflow handle AI-generated code? Anything you'd add — or push back on? Drop it in the comments.*
