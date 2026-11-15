---
tags: [should-i-change-model]
max_turns: 20
timeout_seconds: 600
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

We run a support-triage agent on Sonnet 4.6 (thinking off, no caching). Opus 4.7 just launched, the launch post says it's SOTA on SWE-bench Verified and a bunch of people on X say it's a big jump on agentic tasks. My eng lead wants to switch before next week's release. We handle ~8k tickets a day, p95 latency must stay under 6s. Should we switch?
