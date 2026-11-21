# Evidence First Debugging

A discipline for debugging and fix loops, designed by Muhammad Abdullah Shah with Muse on 2026-11-21. Born from a real incident: hours and roughly 6 to 7 percent of a token budget were spent guessing at a fix, then the hosting logs of the frontend and backend named the cause in 10 minutes. The internet had no answer because the mistake was self inflicted, a configuration error in our own setup that nobody else on earth shares.

## The core rule

- No second fix attempt without a new fact from reality. Before any retry, name the new fact the last evidence pull produced. If you cannot name one, you do not get to spend on another attempt. Guessing twice on the same information is banned.

- Reality is the supreme judge. A fix counts only when reality confirms it: the failing reproduction now passes, the full test suite stays green, the build holds, the deployed surface behaves. Model confidence, including your own, is evidence, never a verdict.

## Declare the blind spots before you start

- List every surface the system touches: local terminal and test output, frontend hosting logs, backend hosting logs, browser network and console, raw JSON response bodies, database and BaaS logs, DNS and CDN configuration, analytics and error monitoring, CI output.

- Mark which surfaces you can actually reach in this session and which you cannot. A missing surface is a declared blind spot. Conclusions drawn with a blind spot open are provisional, and you say so.

- The dangerous blindness is the undeclared one: believing you watched everything when one surface was silently absent. The day of the incident above, a DNS record pointed at a private network address and local tests passed only because the test machine sat inside that network. One surface, checked late, explained everything.

- Self inflicted mistakes are the worst class. The internet is a guesser trained on other people's systems. Nobody else has your exact DNS records, your environment variables, your plan limits. For configuration shaped problems, your own surfaces are the only judge that knows the case.

## Watch at checkpoints, correlate across surfaces

- You are not a daemon with live eyes on dashboards. Watching means evidence pulls at fixed moments: before the first fix attempt, after every failed attempt, and before declaring anything fixed.

- At each pull, gather the reachable surfaces together and lay them side by side before proposing a cause. Errors hide in the mismatch between surfaces, not inside one log. A frontend that reports success while the backend logged a rejection is the diagnosis, not a detail.

- Watch for small early signals: one changed response shape, one slow call, one harmless looking warning. Caught early they point at the cause. Caught after fifty changes they are buried and the trail is dirty.

- Know the division of labour. The agent almost never misses a known kind of abnormal and can hold every surface in attention at once. The agent's blind spots are systematic: a novel pattern outside its picture of abnormal is missed every time, calmly. The human keeps the nose for unnamed wrongness. Write the workflow so each side covers the other.

## The two failure fingerprint

- One failure is noise. The same failure signature twice, after two genuinely different attempts, is a fingerprint: the problem is not in the attempts, it is in your picture of the problem. Same place, same shape, same surviving evidence. Do not judge identity by the error text alone; identical text can hide different causes and different text can share one cause.

- When the fingerprint fires, stop attempting. First research the world's fresh knowledge: provider changelogs, status pages, GitHub issues, releases from the last days. A model knowledge base is old by default and gets older every month. The case may have been patched, renamed, or explained this morning.

- If honest research finds nothing anywhere, treat the silence as a finding: the issue likely arose today or is genuinely new. Switch to first principles. Shrink to the smallest reproduction, change one variable at a time, and map it from zero. Document it well; a truly new issue, written up, is an asset, an upstream report or a post.

## Convert failures into permanent rules

- Every understood failure becomes a regression test, a lint rule, or a checklist line before the task closes. In code, a rule can be a test, and a test enforces itself on every future run at zero cost. This is how a loop improves without trusting its own memory.

- Proposed new rules go on probation: run them against past cases and confirm they would not have rejected work already known to be good. High impact rules need a human signature before they become law.

- Retire rules too. A rule that fires often and is often wrong, judged later by reality, gets demoted or deleted. A rule that never fires is dead weight. Rulebooks that only grow become slow, contradictory, and afraid.

## Stopping and escalation

- Two flat attempts after the fingerprint, or a cost line the user set being crossed, and the loop halts and reports with its evidence instead of burning budget.

- Default scope (user may override): this skill governs debugging and fix loops. It activates on the first failed fix attempt, or immediately when an error crosses the frontend and backend boundary. It does not govern green path building.

- Default disagreement handling (user may override): when surfaces disagree with the leading theory, record the disagreement and proceed on low stakes work. Stop and show the user the evidence before continuing when the disagreement touches production, deployment, DNS, or billing, or when the fingerprint has fired.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Alternatives for better results

- Keep a per project surface inventory in the project notes, listing each platform, how its logs are reached, and which plan limits apply, so the blind spot declaration takes seconds instead of archaeology.

- Pair this skill with the live fixture verdict skill: mocked green tests are unproven, one live run outranks a suite of fakes.
