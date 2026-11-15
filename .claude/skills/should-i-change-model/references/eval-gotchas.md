# Eval gotchas

Most surprising eval results are bugs in the eval, not facts about the model.

| Gotcha | Symptom | Check / fix |
|---|---|---|
| Noise mistaken for signal | Gap between models is inside run-to-run variance | Run every task several times. Require the gap to be many times the standard deviation. High variance often means the task is poorly defined. |
| Infra failures counted as model errors | A model looks bad, but traces show API or tool-call failures | Separate infra errors from model errors before scoring. Read transcripts. |
| Silent saturation | 100% across every config | The eval no longer discriminates. Keep adding harder tasks, and add failures from production. |
| Underspecified tasks | Ambiguous goal or output format; task does not match production | Rewrite until two experts agree on pass/fail. |
| Contaminated environment | Missing or broken dependencies; agent can find the answer externally or in shared state between runs | Reset state between runs. Verify the agent cannot look up answers. |
| Unfair graders | Biased LLM judge, too strict or too lenient, stale or wrong ground truth, a metric the agent can game | Audit grader decisions against transcripts. Update ground truth. |
| Rigid grading | Penalising "96.12" when expecting "96.124991" | Allow reasonable answer shapes and tolerances. |
| Eval misses real gains | One-shot evals underrate a model whose gains show on longer, multi-step tasks | Include longer, complex tasks matching production. |
| Instruction vs. score conflict | Task says optimise to a threshold but grader requires exceeding it, rewarding models that ignore instructions | Make the task text and grader agree. |

## Always read transcripts
Set up tracing (LangSmith, Braintrust or similar) covering system prompts, tool calls and tool results, so you can see exactly what the model saw. One internal eval looked excellent until transcripts showed the agent reading git history from earlier trials to copy the answers. Headline metrics hide this.

## Per-model prompt differences
Every model has quirks. The same prompt can under-trigger a tool on one version and over-trigger it on the next. Read the vendor's prompting guide for the new model and re-tune prompts before judging it.
