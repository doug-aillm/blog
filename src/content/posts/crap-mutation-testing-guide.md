---
title: "CRAP and Mutation Testing: The Missing Checks for Agent-Written Code"
description: "A practical learning guide to CRAP scores and mutation testing: what they measure, why coverage is not enough, and how to use them in a modern code quality loop."
pubDate: 2026-09-12
author: "Doug Aillm"
tags: [software-engineering, testing, code-quality, mutation-testing, ai]
---

Bob Martin posted this morning about agent harness design, and the part worth sitting with is simple: an agent shouldn't be treated like a trusted component in an ordinary software design.

That is the right instinct.

When a person writes code, we lean on taste, review, tests, naming, runtime behavior, and the quiet shame of knowing someone else will read the mess later. When an agent writes code, you still need those pressures, but some of them have to become mechanical.

Two underused checks belong near the front of that conversation:

- **CRAP**, short for Change Risk Anti-Patterns.
- **Mutation testing**, which deliberately breaks your code to see whether your tests notice.

Coverage tells you which lines ran. CRAP tells you where complexity and weak coverage combine into change risk. Mutation testing tells you whether your tests would catch small, realistic bugs.

If you're using coding agents, that difference matters a lot.

## The Problem With "Green Tests"

Green tests feel good. They can also be useless.

Take this tiny function:

```typescript
export function applyDiscount(price: number, percent: number) {
  return price - price * percent / 100;
}
```

And this test:

```typescript
import { expect, test } from "vitest";
import { applyDiscount } from "./discount";

test("applies a discount", () => {
  expect(applyDiscount(100, 0)).toBe(100);
});
```

The test passes. The function is covered. A dashboard might show a happy green bar.

But the test barely tests anything. With `percent = 0`, many broken implementations still pass:

```typescript
export function applyDiscount(price: number, percent: number) {
  return price;
}
```

That is the core trap. Coverage proves execution, not understanding.

This gets worse with generated code because agents are very good at writing tests that look plausible. They often cover the sunny path, mirror the implementation, and miss the boundary that would actually break production.

So we need better questions.

## What CRAP Measures

CRAP stands for **Change Risk Anti-Patterns**. It was created by Alberto Savoia and Bob Evans as a way to quantify code that is risky to change.

The metric combines two signals:

- **Cyclomatic complexity:** how many branches and decision paths a method has.
- **Test coverage:** how much of that method is exercised by automated tests.

The common formula is:

```text
CRAP(m) = comp(m)^2 * (1 - cov(m) / 100)^3 + comp(m)
```

Where:

- `comp(m)` is the method's cyclomatic complexity.
- `cov(m)` is test coverage for that method, from `0` to `100`.

You don't need to memorize the formula. The behavior is the point.

A simple method with good coverage scores low. A complex method with poor coverage scores high. A complex method with strong coverage can still score higher than you'd like because branching logic is harder to reason about.

The classic threshold is around `30`. Above that, a method deserves attention. For agent-written code, I prefer stricter limits because the refactoring cost is lower. If an agent can generate the mess quickly, it can also be forced to simplify it before a human has to care.

## Why Complexity And Coverage Belong Together

Complexity alone can be misleading.

Some code is complex because the domain is complex: pricing, settlement, eligibility, date rules, tax logic, risk checks. A blanket complexity number may flag the code, but it doesn't tell you whether the risk is controlled.

Coverage alone is worse. It rewards tests that walk through code without proving behavior.

CRAP works because it combines the two.

Imagine two methods:

```text
Method A: complexity 3, coverage 90%
Method B: complexity 14, coverage 25%
```

Method B is where you send attention first. It has many paths and weak test exposure. Any change there is a bet.

That makes CRAP useful for triage. It doesn't say, "rewrite this whole module." It says, "start here."

## What Mutation Testing Measures

Mutation testing tests your tests.

It makes small changes to production code, then reruns the test suite. Each changed version is called a **mutant**.

Common mutations include:

- Change `>` to `>=`.
- Change `+` to `-`.
- Change `&&` to `||`.
- Remove a method call.
- Replace a string with an empty string.
- Change `true` to `false`.

Then the tool asks one question: did the tests fail?

If the tests fail, the mutant was **killed**. Good. Your tests noticed the bug.

If the tests still pass, the mutant **survived**. That is a warning. Maybe the mutant is equivalent and doesn't change behavior. Often, though, it means the tests are weak.

Look back at the discount example. A mutation tool could change:

```typescript
return price - price * percent / 100;
```

Into:

```typescript
return price + price * percent / 100;
```

With only the `0%` test, the mutant survives because both versions return `100`.

A better test kills it:

```typescript
test("reduces price by the discount percent", () => {
  expect(applyDiscount(100, 10)).toBe(90);
});
```

Now the bad `+` version returns `110`, and the test fails. That is what you want.

## Coverage Says "Touched." Mutation Says "Proved."

This is the clean mental model:

- Coverage asks whether a line was touched.
- CRAP asks whether risky code has enough test support.
- Mutation testing asks whether the tests detect wrong behavior.

They fit together nicely.

Coverage is cheap and fast, so run it often. CRAP uses coverage plus complexity to tell you where risk is concentrated. Mutation testing is more expensive, so run it on important modules, changed files, or nightly CI.

Trying to replace one with the other is a mistake. They answer different questions.

## A Practical Workflow

Here is a sane loop for a human or an agent:

1. Write or generate the feature.
2. Run the normal tests.
3. Collect coverage.
4. Calculate CRAP scores.
5. Refactor methods with high CRAP scores.
6. Run mutation testing on the changed or high-risk area.
7. Add tests for surviving mutants that represent real missed behavior.
8. Ignore or suppress mutants that are genuinely equivalent or irrelevant.

The discipline is in step 7. Don't add random tests to improve a percentage. Read the surviving mutant and ask, "What behavior did my suite fail to specify?"

That question is gold.

## Tooling You Can Use

For JavaScript and TypeScript, start with StrykerJS:

```bash
npm install --save-dev @stryker-mutator/core
npx stryker init
npx stryker run
```

For .NET, Microsoft's own docs point to Stryker.NET:

```bash
dotnet tool install -g dotnet-stryker
dotnet stryker
```

For Python, `mutmut` is a common starting point:

```bash
pip install mutmut
mutmut run
mutmut results
```

For Java, PIT is the old reliable:

```bash
mvn org.pitest:pitest-maven:mutationCoverage
```

CRAP tooling varies more by language. CodeNarc supports a CRAP metric for Groovy. Older Java tooling included CRAP4J. Many teams now combine complexity reports, coverage reports, and a small custom script in CI.

That is fine. You don't need religious tooling purity here. You need a repeatable signal that says, "This method is complex, poorly tested, and risky to change."

## How To Use This With Coding Agents

Agents change the economics.

A human might resist refactoring a 70-line branchy method because it takes time. An agent has no such excuse. Give it a hard loop:

```text
Run tests and coverage.
Find the highest CRAP-scoring methods touched by this change.
Refactor until each changed method is below the threshold.
Run mutation testing on the changed module.
For each surviving mutant, either add a behavior test or explain why the mutant is equivalent.
Stop only when tests pass and the mutation score meets the threshold.
```

That prompt is much stronger than "write good tests."

The agent now has a scoreboard. Better, it has a scoreboard that punishes shallow tests.

This is where Bob's harness point lands for me. You don't make agents safer by trusting them harder. You make them safer by surrounding them with checks that expose weak work.

## What Not To Do

Don't chase 100% mutation score everywhere.

That sounds disciplined, but it can waste time. Some mutants are equivalent. Some are in logging, generated code, migrations, or adapters where the cost isn't worth it. Microsoft explicitly recommends focusing on high-risk or business-critical areas rather than treating 100% as a universal goal.

Don't worship low CRAP scores either.

A low score doesn't mean the design is good. It means a method is simple enough and covered enough by that metric. You can still have bad names, bad boundaries, duplicated concepts, and a design that fights the domain.

Don't let agents blindly split code into tiny functions just to reduce complexity.

That creates a different problem: code that passes the metric but becomes harder to read. The point is understandable behavior, not a pile of one-use helpers.

## A Good Starting Policy

Start small:

- Coverage on every PR.
- CRAP or complexity-plus-coverage reporting on changed methods.
- Mutation testing only on high-risk modules, changed business logic, and critical bug fixes.
- A nightly mutation run if the suite is too slow for PRs.
- A rule that surviving mutants need an explicit decision: kill, ignore, or document as equivalent.

For agent-written code, tighten the loop:

- Lower the CRAP threshold for changed code.
- Require tests for boundaries, invalid inputs, and state transitions.
- Ask the agent to explain every surviving mutant before changing tests.
- Review the explanation, not just the final green check.

That last part matters. The useful review artifact is not only the diff. It is the reason a mutant survived and the behavior now covered by the new test.

## The Takeaway

Coverage is necessary, but it is not enough.

CRAP points you to risky code. Mutation testing tells you whether your tests would notice a bug. Together, they turn "the tests are green" from a vibe into an argument.

That is exactly the kind of pressure coding agents need.

Let them write code quickly. Then make the harness mean something.

## Sources

- Bob Martin's X post: https://x.com/unclebobmartin/status/2098432570887217520
- Google Testing Blog, "This Code is CRAP": https://testing.googleblog.com/2011/02/this-code-is-crap.html
- CRAP4J FAQ: https://www.crap4j.org/faq.html
- CodeNarc CRAP metric rule: https://codenarc.org/codenarc-rules-size.html#CrapMetric
- Stryker Mutator docs: https://stryker-mutator.io/
- Microsoft Learn, mutation testing with Stryker.NET: https://learn.microsoft.com/en-us/dotnet/core/testing/mutation-testing
