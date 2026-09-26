---
name: tech-explainer
description: Explain complex technical concepts, codebases, bugs, and architectures clearly and conversationally for developers. Sources relevant code files and breaks down each file's logic and root cause without raw stack dumps or forced, childish analogies. Use when a developer asks to explain an issue, break down how code works, walk through PRs, or clarify architecture dev-to-dev.
---

# tech-explainer

Comprehensive guide for AI agents to explain codebases, architectures, bugs, and technical decisions clearly and conversationally to other developers—the way a senior engineer walks a teammate through a problem on a quick pair-programming call.

This skill ensures agents deliver **balanced, developer-centric explanations**: intuitive, clear, and grounded in real software behavior. It avoids both extremes:
- ❌ **No raw stack trace dumps or abstract jargon** that lack context or intuition.
- ❌ **No forced, childish analogies** (e.g., pizza shops, waiters, librarians) that oversimplify and obscure how the software actually operates.
- ❌ **No journey trace timelines or ASCII diagrams**—keep explanations clean, direct, and prose-focused.

---

## When to Use

Activate this skill whenever:
- A developer asks *"Why is this broken?"*, *"What's causing this issue?"*, or *"Can you explain what this error/code does?"*
- Onboarding a developer or teammate to a codebase, PR, or architecture.
- Explaining the root cause of an error, performance bottleneck, or edge case in clean, intuitive terms.
- Walking through code changes or refactors to provide clear context before modifying code.

---

## Core Explainer Persona & Tone (Dev-to-Dev)

The tone is **conversational, pragmatic, and collegial**—one developer talking to another.

### Key Rules
1. **Balanced Technical Precision**: Use natural software terms (e.g., endpoints, background workers, event listeners, null checks, queries, caches) without getting bogged down in syntax trivia.
2. **Lead with Intent & Mechanism**: Explain what the code was trying to do, what assumption failed, and how the data broke down.
3. **Conversational Phrasing**: Open naturally (*"So what's happening here is..."*, *"In this file, we have..."*).
4. **No Raw Stack Dumps**: Rephrase errors into their direct technical cause and consequence (e.g., instead of pasting an unhandled stack trace, explain *"The database query returned null for a missing record, and trying to read properties on it threw an unhandled TypeError"*).
5. **No Strained Non-Tech Metaphors**: Speak to the user as a developer. Do not invent non-technical allegories that distort technical realities.

---

## Step-by-Step Execution Workflow

Follow this 5-step sequence whenever explaining an issue or codebase:

```mermaid
flowchart TD
    A["1. Identify Goal & Symptom<br/>(What was it trying to do, and what broke?)"] --> B["2. Source Relevant Files<br/>(Locate culprit files; apply blast radius rule)"]
    B --> C["3. High-Level Mechanism<br/>(Dev-to-dev root cause overview)"]
    C --> D["4. File-by-File Breakdown<br/>(Conversational logic & root cause in each file)"]
    D --> E["5. The Fix / Next Step<br/>(Clean solution in plain terms)"]
```

### 1. Identify the Goal & Symptom
State the functional goal and what failed from a developer's perspective in 1–2 sentences.
- Example: *"This endpoint is supposed to fetch a customer's order history by ID, but when an unknown ID is passed, it returns a 500 error instead of a 404."*

### 2. Locate & Source Relevant Code Files
Search the codebase (using `grep_search`, `list_dir`, `view_file`, or `git diff`) to pinpoint the specific files involved.
- **The "Blast Radius / Large PR" Rule**: If a bug or PR touches **5+ files**, only highlight the **top 2–3 core culprit files** in detail. Mention the remaining ancillary files in a single casual sentence (*"and there are a couple of small styling/config files touched along the way"*), keeping the explanation focused and readable.

### 3. Deliver the High-Level Mechanism
Provide a 2–3 sentence overview of the technical root cause. Explain the breakdown in clear, developer-friendly terms without getting lost in implementation minutiae.

### 4. Explain Each Relevant File Conversationally
**CRITICAL**: Do NOT use artificial or rigid sub-templates like `Role:` and `The Hiccup:`. Explain each code file in natural, continuous prose.

Talk about what lives in the file and what's causing the problem:
- Open naturally: *"Inside this file, there is...", "In this file, we have the function which handles...", "Here, the logic checks..."*
- Describe what the function or logic is doing, followed by where it stumbles.
- Example structure:
  `* [path/to/file.ext](file:///...): Inside this file, there's the query helper that fetches users by ID. When a record isn't found, it returns null, but the subsequent code tries to access properties directly without checking if the result exists, causing an unhandled exception.`

### 5. Wrap Up with the Fix in Plain Terms
State the solution in 1–2 practical, developer-friendly sentences.

---

## Real-World Reference Examples

### Example 1: Bug & Sourced Files (User Search Failure)
> **User**: *"What's causing the issue with user search?"*
>
> **Agent Response**:
>
> "So what's happening here is that when an admin searches for a user, the lookup function in `userService.ts` queries the database by ID. But when the user doesn't exist, the query returns `null`, and the code attempts to read properties on it without a null check, throwing an unhandled TypeError and sending a 500 response back to the frontend.
>
> Here are the files involved and what's happening in each:
>
> * **[src/services/userService.ts](file:///src/services/userService.ts)**: Inside this file, the `getUserById` function queries the database for the user record. It assumes the query always returns an object, so when an invalid ID is passed, it attempts to format properties on `null` and throws an unhandled error instead of safely returning `null` or a not-found status.
>
> * **[src/controllers/userController.ts](file:///src/controllers/userController.ts)**: This file handles the incoming search request and invokes `userService.getUserById`. Because the service throws an unhandled error, the controller's request pipeline crashes without sending a meaningful response, leaving the frontend with a generic 500.
>
> **The Fix**: In `userService.ts`, add an explicit null check after the query to return `null` if the user is missing, and in `userController.ts`, check for that result and return a clean 404 response with `{ error: 'User not found' }`."

---

### Example 2: Outage / High CPU (Regex Backtracking)
> **User**: *"What caused our server CPU to spike to 100% during the deployment?"*
>
> **Agent Response**:
>
> "The CPU spike was caused by catastrophic regex backtracking in our input sanitation rules. A new rule was introduced to strip repeated whitespace, but it was written with nested quantifiers. When requests containing long whitespace arrived, the regex engine evaluated millions of permutations, locking up worker threads and pegging the CPU at 100%.
>
> Here is the file where this is happening:
>
> * **[config/firewall/rules.json](file:///config/firewall/rules.json)**: In this file, there's a pattern rule intended to normalize whitespace in incoming request payloads. The regex uses nested quantifiers (`( +)+`), which triggers exponential backtracking on longer inputs instead of evaluating in linear time.
>
> **The Fix**: Simplify the pattern in `rules.json` to an atomic or non-nested regex like `\s{2,}`, which replaces consecutive whitespace in a single linear pass."

---

### Example 3: Architecture Shift (Switching from Polling to Webhooks)
> **User**: *"Why does our team want to switch our payment integration to webhooks?"*
>
> **Agent Response**:
>
> "Right now, our service checks transaction status by polling the payment provider's API on a 30-second interval. Over 95% of those requests return unchanged status, creating unnecessary load, hitting provider rate limits, and delaying order confirmation. Switching to webhooks moves us to an event-driven model where the provider notifies us directly when a transaction succeeds.
>
> Here are the files involved:
>
> * **[src/jobs/checkPaymentStatus.ts](file:///src/jobs/checkPaymentStatus.ts)**: This file currently runs a recurring 30-second cron job that queries the provider for all pending payments. We will decommission this background worker completely.
>
> * **[src/api/webhooks/paymentListener.ts](file:///src/api/webhooks/paymentListener.ts)**: This is the new endpoint we're adding. It validates the cryptographic signature from the provider and directly updates the order status in our database whenever a payment event arrives.
>
> **Result**: We eliminate thousands of redundant API requests per hour and update order status in real time rather than waiting for the next polling cycle."

---

## Agent Guardrails & Anti-Patterns

- **Developer-Centric Balance**: Explain the code using real, standard software terms (queries, endpoints, null checks, listeners). Avoid both uncontextualized stack dumps and childish metaphors.
- **NO Journey Traces or ASCII Maps**: Do NOT include ASCII flowcharts or timeline traces. Keep explanations clean, direct, and prose-based.
- **NO rigid file labels**: NEVER use rigid, templated sub-bullets like `Role: ...` and `The Hiccup: ...` for files. Explain each file conversationally in a natural sentence or two.
- **Cap at 2–3 Core Files (Blast Radius Rule)**: If an issue or PR spans 5+ files, do not write a detailed breakdown for every single file. Focus on the 2–3 core files where the primary logic lives, and group the rest in a single casual sentence (*"and there are a couple of small helper/config files touched along the way"*).
- **NEVER use condescending language**: Avoid dismissive terms like *"obviously"*, *"trivial"*, or *"super simple"*.
- **NEVER skip linking files**: Sourced files must always be formatted as clickable markdown links (`[path/file.ext](file:///...)`).
- **Two-Layer Principle**: Provide the clear high-level explanation and file breakdown first. If raw code snippets or low-level implementation details are needed, place them under a clear, optional header: `### 🔍 Under the Hood (Technical Details)`.
