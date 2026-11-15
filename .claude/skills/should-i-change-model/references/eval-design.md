# Designing the eval

A **task** is the atomic unit: inputs, success criteria, graders. An **eval** is a set of tasks. Think of it as a maths exam: the answer matters, and so does showing the working. For agents, check both that the final outcome is right and that the right steps were taken.

## A well-defined task
Two independent experts would reach the same pass/fail verdict from the scoring. If they wouldn't, rewrite it.

- **Start small and realistic:** 20 to 50 real examples that are fast to run. Early on the effects you care about are large, so a small representative sample is enough. Pull them from real user requests, support tickets and past failures.
- **Right grader:** Use an LLM judge where several answers can be right (e.g. SQL that differs in syntax but returns the same data). Use deterministic code checks for hard rules (e.g. the agent always calls the search tool and always passes the customer's country). Do not assert one exact tool-call sequence: agents find valid routes you did not anticipate.
- **Failures are diagnostic:** when a task fails it should be clear why. Keep a reference solution for every task to prove it is solvable.
- **Isolate capabilities:** one task points at one skill, not a chain of five. Test both directions, or you optimise one way.

## Task template
```
id:
source: (ticket / log / hand-written)
input:
expected outcome:
required steps / tools:
forbidden behaviours:
graders: [code check | LLM judge] + what each checks
reference solution:
capability tested:
```

## Example: customer-service agent
- LLM judge: final reply matches the expected response.
- LLM judge: agent queried the database correctly to find the customer (tolerates syntax differences).
- Code check: search tool called, with the country argument set to the customer's country.

## Keeping it alive
After launch, collect traces, find where the agent fails or what users actually ask, and add those cases to the eval set so it stays representative.
