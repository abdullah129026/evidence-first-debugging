---
name: evidence-first-debugging
description: Evidence-first debugging discipline for coding agents. Use whenever you are debugging a failure: a bug report, a failing test, a broken build, a production error, a flaky test, or a performance regression. Enforces reproduction before fixing, evidence pulls from every reachable surface (logs, terminal, network, responses, config), a falsifiable prediction for every fix attempt, and a ban on retrying a fix without a new fact.
---

# Evidence First Debugging

## When this applies

Any debugging task, from the moment an error or failure is being investigated. Not green-path feature building. For a trivial single-file error with an obvious cause, use the short loop: reproduce, one attempt with a prediction, verify.

## Step 0: Reproduce before you fix

- Reproduce the failure and write down the exact reproduction: command, input, or steps, plus the observed failure.
- No reproduction, no fix attempt. If you cannot reproduce it yet, your first job is a reproduction, and your evidence pulls aim at making it reproducible.
- For a flaky test, the reproduction is a repeated run with a measured failure rate. For a performance regression, it is a baseline measurement.

## Step 1: Inventory the surfaces, declare the blind spots

- List the surfaces this system actually has, taken from the system in front of you, not from a template: terminal and test output, application logs, frontend and backend hosting logs, browser network and console, raw response bodies, database logs, DNS and CDN configuration, CI output, monitoring. For a CLI: exit codes, stderr, state files. For a data pipeline: run history and per-step logs. For mobile: device logs and crash reports.
- Mark each surface reachable or unreachable in this session. Reach them with the tools at hand: hosting CLIs or dashboards (for example `vercel logs`, Render or Fly log views), `curl` for endpoints, `dig` or `nslookup` for DNS, the browser for network and console, the CI provider's log view.
- For every unreachable surface that could hold the cause, ask the user for it: paste the log, run the command, check the dashboard. Do not proceed silently past a blind spot. If you must proceed, label every conclusion provisional and name the blind spot in your report.

## Step 2: The attempt loop

Every fix attempt uses this template, written out before the attempt:

- **Surfaces pulled:** which surfaces you read for this attempt.
- **New fact:** a concrete observation from a real surface that was not available before the previous attempt and that narrows the cause. A restatement, a guess, or a trivial detail does not count.
- **Hypothesis:** the cause you are testing.
- **Prediction:** if this hypothesis is right, then this change will produce this observable result. If the result does not appear, the hypothesis is wrong.
- **Attempt:** one change that tests the hypothesis.
- **Result:** what reality said.

Rules:

- No second attempt on the same failure without a new fact in the template. There is no low-stakes exception for the failing thing itself. You may keep investigating or work on something else, but you may not re-attempt the fix on old information.
- One change per attempt. After a failed attempt, revert its changes before the next attempt, unless the template records evidence that keeping them is part of the next hypothesis. Keep the trail clean so each result means something.
- A fix counts only when the Step 0 reproduction now passes and the surrounding checks stay green: the full test suite when practical; when it is not, the affected subset plus a smoke check, and say which you ran. When no tests exist, the reproduction script from Step 0 is the check.
- Use model pattern recognition for triage and hypotheses. It is never the verdict on a fix. Only surfaces and reproductions give verdicts.

## Step 3: The two failure fingerprint

- **Failure signature:** the same failing reproduction step, the same error class and location, and the same evidence state after the attempt. Do not judge identity by error text alone; identical text can hide different causes, and different text can share one cause.
- **Genuinely different attempts:** attempts that test different hypotheses, not the same hypothesis with cosmetic code changes.
- Same signature twice, after two genuinely different attempts, means your picture of the problem is stale. Stop attempting.
- First, research fresh external knowledge: changelogs, release notes, status pages, issues and fixes from recent days. A model's built-in knowledge is old by default and gets older every month. Check your search quality before trusting silence: multiple queries, primary sources. Silence after good searching is weak evidence that the issue may be new; hold it as a working hypothesis, not a conclusion. If there is no web access, note that and move on.
- Then switch to first principles: shrink the reproduction to the smallest case that still fails, change one variable at a time, and build the explanation from observations.
- Configuration and environment causes (your own DNS, environment variables, plan limits, credentials) are common and unsearchable, so check them early. Do not let that become a bias: when the evidence points at code, follow it.

## Step 4: Close the loop

- Convert every understood failure into a permanent guard before closing: a regression test where tests exist; where they do not, keep the minimal reproduction script with the project, or add a checklist line to the project notes.
- A regression test that keeps passing is doing its job. Never retire a guard for not failing. Retire or fix advisory checklist rules that repeatedly fire wrongly.
- Broader process rules you invent along the way go into the project notes as proposals for the human. They do not come into force on your own authority.

## Stopping and escalation

- A **flat attempt** is one that produced no new fact and no change in the failure signature.
- After the fingerprint fires, two flat attempts means stop. Report and ask, with these contents: the reproduction, the surfaces pulled, the declared blind spots, each hypothesis with its prediction and result, the current failure signature, and the specific thing you need (access, a log, a decision) to continue.
- If the session has a known token or time budget, respect the user's cap. When none is known, the attempt-count rules above are the cap.
