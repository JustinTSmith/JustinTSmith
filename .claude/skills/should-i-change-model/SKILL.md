---
name: should-i-change-model
description: Decide whether to switch, upgrade, or downgrade the model (or its effort/thinking/caching config) behind an AI agent or LLM feature, and produce a go/no-go decision memo plus an eval plan. Use whenever the user asks "should we switch to the new model", "is Opus/Sonnet/Haiku worth it", "a new model dropped, should we upgrade", "which model should I start with", "can we go cheaper or faster", "compare models for our agent", or shares benchmark results or launch hype and wants to know what it means for their product. Trigger even if they never say "eval". Do not use for choosing a model for a one-off personal task.
---

# Should I change the model?

Turn "a new model dropped" into a repeatable, evidence-based yes/no. Launch posts, public benchmarks and social media tell you about models in general. They do not tell you about this user's workload, so the answer has to come from a small eval built from their real tasks. This skill gets the user to that eval and to a decision rule they commit to before seeing results. It plans the decision and does not run the models unless the user asks.

The user is often a PM or builder who owns the product outcome. Keep it executive-level and short unless they ask to go deeper. Be direct: if their current evidence (a benchmark chart, a gut feel, one demo) cannot support a switch, say so.

## Workflow

1. **Frame the decision** (ask, don't assume; use AskUserQuestion if available).
   - What does the agent/feature do, and who uses it?
   - Current model and config (model, effort/thinking, caching, context setup).
   - Candidate(s): new model, other tier, other provider, or a config change only.
   - Why now: new launch, cost pressure, latency complaints, quality complaints, greenfield pick.
   - Which of **quality, latency, cost** matters most, and any hard limits (e.g. p95 under 3s, under $0.05 per task).
   - What evals, traces or production logs already exist.

2. **Check the evidence they have.** Public benchmarks (SWE-bench, BrowseComp, GPQA, etc.) are priors, not verdicts: they cover narrow slices, and scores move with scaffolding and formatting. Use them only to decide which candidates are worth testing. If they only have hot takes or a benchmark chart, the answer is "not yet, build the eval".

3. **Design the eval** from `references/eval-design.md`: 20 to 50 realistic tasks from real requests and failures, each with inputs, expected outcome, required steps, and graders. Grade the working as well as the answer. Include tasks that test both directions (when the agent should act and when it shouldn't).

4. **Pre-commit the decision rule** before any results. Define:
   - The three metrics: quality (task success rate), latency (p50 and p95), and **cost per successful outcome** (total spend divided by successes, not price per token).
   - A threshold for each, and what combination means switch, stay, or run more tests.
   - Runs per task (at least 3) and how big a gap counts as real: it should be many times the run-to-run standard deviation. With 20 to 50 tasks you can see large effects, not small ones, so close results mean "need more tasks or runs".

5. **Plan the config grid.** Model tier is only one dial. List the combinations to test: model x effort/thinking x caching x context setup. Warn that a smaller model is not automatically faster or cheaper per outcome, because stronger models often finish in fewer turns with fewer failures. Test it.

6. **List the eval pitfalls to check** before trusting results, using `references/eval-gotchas.md`. Lead with the ones most likely for this user's setup.

7. **Plan the read-through.** Someone must read failure transcripts before reading averages. Confirm tracing captures system prompt, tool calls and tool results. Name the prompt-tuning step: read the vendor's prompting guide and re-tune prompts per model, since the same prompt can under- or over-trigger a tool across versions.

8. **Write the memo** in the format below. If results already exist, apply the decision rule to them and say what is and isn't established.

## Output format

Use this structure, kept to about one page:

```
# Model decision: [feature] - [current] vs [candidate(s)]

## Recommendation
[Switch / Stay / Not enough evidence yet] - one or two sentences on why.

## What we know vs. don't know
- Evidence in hand and how much weight it deserves
- Gaps

## Decision rule (set before results)
| Metric | Current | Switch if... |
Quality / Latency p50-p95 / Cost per successful outcome / Runs per task

## Eval plan
- Task sources, count, owner, deadline
- Graders (LLM judge vs code check, per task type)
- Config grid to test

## Risks to the result
- Top 3 pitfalls from the gotchas list that apply here

## Next 3 steps

## Assumptions
[Anything you assumed because the user didn't say. Mark thresholds and sample-size advice as your judgement.]
```

End by offering to go deeper on any section (task templates, grader design, reading results).

## Principles

- Cost per successful outcome beats cost per token.
- Surprising results are more often eval bugs than facts about the model. A 0% pass rate usually means a broken task. 100% everywhere means the eval is saturated.
- Do not let the user rationalise: thresholds are set first.
- Say plainly when you are adding judgement beyond the evidence (for example sample-size advice), so the user can tell what is sourced from the eval practice and what is your view.
