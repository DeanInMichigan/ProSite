---
title: "Chapter 3: The Baseline Rules for AI-Assisted Development"
description: "Twenty practical rules for individual developers and teams — covering prompting standards, code review, security, data handling, and quality gates."
tag: "AI & Development"
order: 6
---

# Chapter 3: The Baseline Rules for AI-Assisted Development

In [Chapter 1]({{ '/articles/enterprise-ai-dev-rules/' | relative_url }}), we covered the ten enterprise rules for deploying AI coding tools safely. In [Chapter 2]({{ '/articles/enterprise-ai-dev-workflow/' | relative_url }}), we walked through a complete development workflow that integrates AI at every stage.

Now we get specific.

This is the baseline ruleset — the ground-level rules that individual developers and teams should follow on every project, every day. These aren't policies for the executive deck. These are working rules: practical, numbered, and each one paired with the reason it exists.

Organize them however makes sense for your team. Put them in your developer handbook, your onboarding checklist, your repo's CONTRIBUTING.md. But get them written down, agreed upon, and enforced. Because informal norms don't survive team growth, deadline pressure, or the sheer speed at which AI tools are evolving.

---

## Part 1: Prompting Standards

These rules govern how developers interact with AI tools. Quality in, quality out — but also safety in, safety out.

---

**Rule 1: Always provide context about purpose, not just the task.**

*The rule:* When prompting an AI tool, explain what the code is for, not just what you want it to do. Include the language, framework, relevant libraries, and version constraints.

*Why it matters:* A prompt like "write a login function" will produce something generic, potentially insecure, and almost certainly wrong for your specific stack. A prompt like "write a login action in ASP.NET Core 8 using ASP.NET Core Identity with bcrypt password hashing, returning a signed JWT — the app uses a global exception handler middleware and error responses must follow RFC 7807 problem details" produces something you can actually use. Garbage prompts produce garbage code. Context is not optional.

---

**Rule 2: Never include secrets, credentials, or PII in a prompt.**

*The rule:* Strip all API keys, database passwords, tokens, personally identifiable information, and proprietary data from any code or context you paste into an AI tool. Always. Without exception.

*Why it matters:* Every prompt you send is processed on a remote server. Even in enterprise-tier tools with strong data handling agreements, you are increasing the surface area for potential exposure. Beyond technical risk, this is a compliance issue: depending on your industry, passing PII to a third-party AI service without proper data processing agreements may be a regulatory violation. The rule is simple: if it's sensitive, it doesn't go in the prompt.

---

**Rule 3: Specify security and error handling requirements explicitly.**

*The rule:* Don't assume the AI will include input validation, error handling, or security controls. Ask for them by name.

*Why it matters:* AI tools optimize for code that works in the happy path. They will give you a function that runs — but it may not validate inputs, handle edge cases, or fail gracefully. Unless you ask for it, you probably won't get it. Prompts like *"include input validation and return a 400 with a descriptive error if the input is invalid"* or *"this function handles user-uploaded files — include path traversal protection"* produce significantly safer output.

---

**Rule 4: When asking for refactoring, state the reason.**

*The rule:* When using AI to refactor existing code, specify why — is it for performance, readability, testability, security, or reducing complexity?

*Why it matters:* Refactoring for readability and refactoring for performance are different operations that can produce contradictory results. Without a stated goal, the AI will make judgment calls you didn't ask for. You may end up with technically correct code that doesn't serve the purpose of the change — and a reviewer who can't tell what the refactor was even trying to accomplish.

---

**Rule 5: Treat multi-step prompting as a discipline.**

*The rule:* For complex tasks, break your prompting into stages: design first, then implement, then test. Don't ask for everything in one prompt.

*Why it matters:* One-shot prompts for complex features produce bloated, poorly-structured output that's hard to review and harder to debug. Walking through the problem in stages — *"here's the data model, does this design make sense?"* then *"now implement the persistence layer"* — produces better code and forces the developer to actually think through the problem rather than outsourcing the thinking entirely.

---

## Part 2: Code Acceptance & Review

These rules govern what happens between AI output and a merged commit.

---

**Rule 6: Read every line before you accept it.**

*The rule:* No auto-accepting suggestions without reading them. Every line of AI-generated code that enters your codebase must be understood by the person accepting it.

*Why it matters:* GitHub Copilot's inline suggestions are fast and often feel obviously right. That feeling is a trap. Studies of AI-generated code consistently find security vulnerabilities in a significant percentage of output — vulnerabilities that look fine at a glance. The developer who accepts the suggestion owns the code. Owning code you don't understand is a liability.

---

**Rule 7: If you can't explain it, you can't ship it.**

*The rule:* If a block of AI-generated code does something you can't explain to a colleague, that's a blocker — not a question for later. Stop, understand it, or rewrite it.

*Why it matters:* This rule sounds obvious but is routinely violated under deadline pressure. "The AI seemed confident" is not a code review. The inability to explain code is a signal that you don't understand its failure modes, its edge cases, or what happens when it receives unexpected input. That's exactly the kind of code that creates incidents six months later.

---

**Rule 8: Tag all AI-assisted commits.**

*The rule:* Any commit containing significant AI-generated code should be tagged in the commit message — a simple `[AI-assisted]` prefix is sufficient.

*Why it matters:* This creates an audit trail without adding meaningful overhead. When a bug surfaces or a security issue is discovered, knowing that a specific commit was AI-assisted tells the reviewing team to apply additional scrutiny and helps identify whether similar patterns exist elsewhere in the codebase. Regulators are beginning to ask about AI usage in development pipelines. Getting ahead of the documentation requirement is far easier than reconstructing it after the fact.

---

**Rule 9: AI-generated code gets the same PR process as human-written code.**

*The rule:* No fast-tracking, no self-merging, no "it's just boilerplate." The full pull request process applies to all code, regardless of origin.

*Why it matters:* The temptation to shortcut code review for AI-generated output is real — it feels like it's already been "checked." It hasn't. The PR process exists to catch what individuals miss. AI tools miss plenty. Branch protection rules, required reviewers, and status checks are non-negotiable regardless of who or what wrote the code.

---

**Rule 10: Apply heightened scrutiny to security-sensitive code.**

*The rule:* Authentication, authorization, session management, data access controls, cryptography, and input handling require senior review — always, but especially when AI-generated.

*Why it matters:* These are the areas where subtle mistakes have the highest impact, and they are also the areas where AI tools most frequently produce code that is plausible but wrong. An authentication function that compiles and runs correctly may still have a timing vulnerability. An authorization check may have an edge case that's trivially exploitable. In security-sensitive areas, "looks right" is not the bar. The bar is "I have carefully considered the failure modes."

---

## Part 3: Security & Data Handling

These rules govern how AI tools interact with your data, your infrastructure, and your dependencies.

---

**Rule 11: Classify data before prompting — every time.**

*The rule:* Before pasting any code or data into an AI tool, determine its classification. Public and internal data can generally go to approved cloud-based tools. Sensitive, regulated, or proprietary data requires an on-premise or air-gapped solution.

*Why it matters:* Data classification isn't a one-time exercise. It's a decision made at the moment of every prompt. Developers who don't have clear guidance on what can and can't go into a cloud AI tool will make inconsistent choices — and some of those choices will be wrong in ways that create compliance exposure. Make the classification decision tree simple enough to follow in real time.

---

**Rule 12: Scan every dependency AI suggests before importing it.**

*The rule:* When an AI tool recommends a library or package, verify it independently before adding it to your project. Check for known CVEs, confirm the package is actively maintained, and validate that the name matches the legitimate package.

*Why it matters:* AI tools suggest packages based on training data, not real-time vulnerability databases. They also occasionally suggest packages with names similar to legitimate ones — a pattern attackers exploit deliberately (typosquatting). A single compromised dependency can expose your entire application. Automated dependency scanning in your CI/CD pipeline is essential, but developer-level awareness at the point of import is your first line of defense.

---

**Rule 13: Treat AI-suggested infrastructure as untrusted.**

*The rule:* AI-generated infrastructure code — Terraform, CloudFormation, Kubernetes manifests, Dockerfiles — must be reviewed by someone with infrastructure security expertise before deployment.

*Why it matters:* Infrastructure misconfigurations are one of the leading causes of data breaches. AI tools will produce infrastructure code that works but may include overly permissive IAM roles, open security groups, unencrypted storage, or default credentials. Infrastructure code that passes functional tests can still be a security disaster. This category of AI output requires a reviewer who knows what they're looking for — not just someone who can confirm it runs.

---

**Rule 14: Use only approved tools for the relevant data classification.**

*The rule:* Maintain an approved list of AI tools for each data classification tier. Developers should not choose their own tools ad hoc.

*Why it matters:* Not all enterprise AI tools have equivalent data handling commitments. Some process data offshore. Some retain prompts. Some use inputs for model training by default. Your security and legal teams need to evaluate and approve tools before they touch your data — not after. Shadow AI (unapproved tools used without IT oversight) is growing fast and represents a real compliance and breach risk.

---

## Part 4: Testing & Quality Gates

These rules ensure AI-generated code meets the same quality bar as everything else in your codebase.

---

**Rule 15: Coverage thresholds apply to AI-generated code — no exceptions.**

*The rule:* Your project's minimum test coverage requirements apply to all code, regardless of whether it was written by a human or an AI.

*Why it matters:* Coverage requirements exist because untested code is a liability. That logic doesn't change based on who wrote the code. If anything, AI-generated code benefits more from thorough testing because the developer may have a shallower understanding of its implementation details. Coverage thresholds are not optional and do not have an "AI wrote it" carve-out.

---

**Rule 16: AI should not write the only tests for its own code.**

*The rule:* When AI generates a function, a human should write or meaningfully review the tests for that function. AI-generated tests for AI-generated code require independent human validation of both.

*Why it matters:* An AI tool asked to both implement a feature and test it will tend to produce tests that validate its own assumptions. If the implementation has a logical flaw, the tests may be written in a way that enshrines that flaw rather than catching it. Independent human review — either writing the tests from scratch or critically evaluating AI-generated tests against the actual requirements — breaks this feedback loop.

---

**Rule 17: All CI/CD quality gates are hard gates.**

*The rule:* If the pipeline fails, the merge doesn't happen. No overrides, no "I'll fix it in the next PR," no exceptions for time-sensitive releases.

*Why it matters:* The entire value of automated quality gates is that they are consistent and non-negotiable. A gate that can be overridden under pressure is not a gate — it's a suggestion. This is especially important for AI-assisted code, where the temptation to trust the output and skip the process is highest. Build the culture now: the pipeline is the authority, not the deadline.

---

**Rule 18: SAST runs on every AI-assisted change.**

*The rule:* Static Application Security Testing is mandatory for every pull request that includes AI-generated code. The results must be reviewed, not rubber-stamped.

*Why it matters:* Research consistently shows that AI-generated code introduces security vulnerabilities at a rate that warrants automated scanning on every change. SAST tools like Snyk, Semgrep, or GitHub Advanced Security will catch a significant proportion of these before they ever reach production. The key word is *reviewed* — a SAST scan that produces warnings which are bulk-dismissed provides no protection.

---

**Rule 19: Performance-sensitive code requires profiling, not assumptions.**

*The rule:* When AI generates code for performance-sensitive paths — high-throughput APIs, data processing pipelines, real-time systems — profile it before shipping it. Don't assume it's efficient because it looks clean.

*Why it matters:* AI tools optimize for readable, idiomatic code. They do not optimize for your specific performance requirements, your data volumes, or your infrastructure constraints. Code that looks elegant may have hidden O(n²) behavior, unnecessary database round-trips, or memory patterns that degrade under load. Profiling is not optional for performance-sensitive work.

---

**Rule 20: Document deviations from these rules.**

*The rule:* If a team decides to waive or modify any of these baseline rules for a specific project or context, that decision must be documented — who approved it, why, and what compensating controls are in place.

*Why it matters:* Rules that can be quietly ignored aren't rules. Documentation of deviations creates accountability and a paper trail. It also surfaces patterns: if the same rule is being waived repeatedly, that's a signal the rule needs to be revisited — or the team needs more support in following it. Either way, you can't improve what you can't see.

---

## 💡 Tip: Use a CLAUDE.md File to Encode These Rules Directly

Posting a ruleset in a wiki is a start. But there's a more direct way to enforce it — one that puts the rules into the AI's working context every time a developer opens the project.

If your team uses Claude for development (via Claude Code or similar), a **`CLAUDE.md`** file in the root of your repository acts as persistent instructions that Claude reads at the start of every session. Think of it as a standing brief: before Claude writes a single line, it already knows your team's standards, your forbidden patterns, and your required conventions.

Here's what that looks like in practice. A well-written `CLAUDE.md` might include:

**Project standards Claude should always follow:**
```
- Never store secrets or credentials in code. If a secret is needed, reference
  IConfiguration or environment variables and note it in appsettings.example.json.
- Always include input validation on public-facing methods. Use FluentValidation
  or Data Annotations — whichever is already in use in this project.
- Error responses must follow RFC 7807 problem details format using
  ASP.NET Core's built-in ProblemDetails.
- New NuGet packages require a comment in the PR noting why the dependency was added.
```

**Patterns Claude should flag or avoid:**
```
- Flag any use of dynamic SQL string concatenation and explain the SQL injection risk.
  Always suggest parameterized queries or Entity Framework instead.
- Do not suggest suppressing Roslyn analyzer warnings (pragma warning disable)
  inline. If a rule needs revisiting, note it for the team to discuss.
- Never suggest storing tokens or sensitive session data in plain text,
  unprotected configuration files, or in-memory caches without expiry.
- Flag any use of unsafe reflection or dynamic type invocation and explain the risk.
```

**Project context that improves output quality:**
```
- This project uses .NET 8, ASP.NET Core, and SQL Server 2022.
- Authentication uses ASP.NET Core Identity with JWT bearer tokens.
  See /src/Identity for existing patterns.
- Follow the error handling conventions in /src/Middleware/GlobalExceptionHandler.cs.
- Dependency injection is configured in /src/Extensions/ServiceCollectionExtensions.cs.
```

The result is that your baseline rules aren't just documented — they're *active*. Claude will follow them, reference them, and flag deviations from them, automatically, on every task in that repository.

This doesn't replace human review. But it does mean the AI is working from your team's playbook, not its own defaults — and that's a meaningful difference in the quality and consistency of what it produces.

For teams rolling out AI coding tools, building a `CLAUDE.md` template as part of your standard repo scaffold is one of the highest-leverage things you can do. Write it once, update it as your standards evolve, and every new project starts with the guardrails already in place.

---

## Making This Stick

A ruleset is only as good as the culture that enforces it. Post this where your developers work — in your README, your developer portal, your onboarding docs. Review it quarterly as the tools evolve. And when someone breaks a rule, treat it as a process problem first: is the rule clear? Is it easy to follow? Is the tooling in place to support it?

The goal isn't compliance for its own sake. The goal is building software that's secure, reliable, and maintainable — with AI as a genuine accelerator, not a liability waiting to surface.

---

*Previously: [Chapter 2: Building an Enterprise AI Development Workflow]({{ '/articles/enterprise-ai-dev-workflow/' | relative_url }})*

*Next up in this series: how to measure whether your AI-assisted development program is actually working — the metrics that matter and the ones that mislead.*

*Which of these rules does your team already follow? Which ones would push back on? I read every comment.*
