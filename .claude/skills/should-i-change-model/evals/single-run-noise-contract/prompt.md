---
tags: [should-i-change-model]
max_turns: 20
timeout_seconds: 600
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

we already ran an eval on our contract-review agent, 30 tasks, 1 run each. Haiku 4.5 got 83%, Sonnet 4.6 got 87%, Opus 4.7 got 90%. Opus costs about 5x per token. Haiku with thinking on hasn't been tried. Can we just go with Opus since it's the best?
