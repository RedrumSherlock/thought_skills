---
name: signal-noise
description: Sort the data collected during research into evidence and noise for one hypothesis. Keep only items that change how likely the hypothesis is, test which ones show cause and effect, update the odds step by step, then have an independent sub-agent judge the result. Based on Nate Silver's The Signal and the Noise.
disable-model-invocation: true
---

# Signal and Noise

The signal is what is true about the hypothesis. The noise is everything that distracts from it. More data brings more noise, not more signal: with more data it is easier to pick the items that fit what you already believe, and to see patterns that are not there.

Input: $ARGUMENTS

The data is everything collected in this conversation (search results, pages, papers, numbers, logs), plus any files or links given above. If no data has been collected yet, search for it first.

Run the six steps in order. Do not skip a step.

**You, the agent, do all six steps yourself. Never hand a step to the user.**

Rules for every step:

- Weigh many small, independent pieces of evidence. Do not let one big story decide.
- Be most careful with items that fit the answer you or the user would like.
- Give ranges, not single numbers.
- Change the answer when the evidence says so.

## Step 1: State the hypothesis and the prior

1. Write the hypothesis as one testable cause → effect claim: what causes what, for whom or where, over what time, measured how. If none is given, take it from the conversation and state it.
2. List the main rival explanations: what else could produce the same effect.
3. Set the prior before you weigh the data. The prior is how likely the hypothesis is before this evidence. Base it on the base rate: how often claims like this turn out true, or how often this effect happens in similar cases. Give a range, such as 20–40%. If there is no real base rate, say the prior is a guess.

## Step 2: List every item

Break the data into single claims, one per row. Split a mixed statement such as "X rose because of Y" into its fact ("X rose") and its cause claim ("because of Y").

Trace each item to its original source: the person or group who measured or observed it. Fetch pages when needed. Record the date, what was measured, and the sample size or time span.

Merge items that repeat one original source. Ten articles that cite one study are one piece of evidence, not ten.

| ID | Claim | Original source, date | What was measured (sample, time span) |
|----|-------|-----------------------|---------------------------------------|
| D1 | ...   | ...                   | ...                                   |

## Step 3: Remove the noise

Test each item. An item that fails any of tests 1–4 is noise. Record which test it failed.

1. **Does it change the odds?** Ask: how likely is this item if the hypothesis is true, and how likely if it is false? If the two are about the same, the item is noise, even if it is true and on topic.
2. **Is it an observation?** Measurements, experiments, records, and documented events count. Opinions, forecasts, commentary, marketing, and claims with no traceable source are noise. They can show where to look, but they are not evidence.
3. **Is it bigger than normal variation?** Compare the change with how much the measure moves on its own. A single spike, a short time window, a small sample, or one extreme value followed by a return to normal is noise unless it repeats.
4. **Was it found by searching many variables?** When many possible links are checked, some match by chance. Treat such a pattern as noise unless it has a known mechanism and also holds in data that was not used to find it.
5. **Who made it, and what do they gain?** A source paid by an interested party, or rewarded for a certain answer, is not noise for that reason alone. Lower its weight and note the interest.

Keep items that count against the hypothesis. They are signal too.

## Step 4: Test cause and effect

For each item left, check whether it shows that the cause changes the effect. These checks follow Bradford Hill's tests for causation (1965), and the book's point that a pattern without a known cause is easy to overfit.

- **Time order:** the cause comes before the effect.
- **Comparison:** there is a group or period without the cause to compare against. An experiment is stronger than a natural experiment, which is stronger than a plain observation, which is stronger than a single story.
- **Other explanations ruled out:** a third factor that drives both, the effect causing the "cause", who was counted (selection), chance, and regression to the mean (an extreme value followed by a more normal one by chance).
- **Mechanism:** a known process by which the cause produces the effect.
- **Dose:** more of the cause gives more of the effect.
- **Repeat:** independent sources, places, or times show the same result.
- **New data:** the link held in data not used to find it, or a prediction made from it came true.
- **Size:** the effect is large enough to matter.

Rate each item:

| Rating | Meets |
|--------|-------|
| **Direct cause** | Time order, comparison, other explanations ruled out, and at least one independent repeat |
| **Likely cause** | Time order and mechanism, consistent across sources, but no clean comparison |
| **Correlation only** | The two move together, but the cause is not shown |

A "correlation only" item is a lead, not a cause. Never call it a cause in the result.

## Step 5: Update the odds

Start from the prior in Step 1. Take the items from strongest to weakest. For each one, ask: how many times more likely is this item if the hypothesis is true than if it is false?

| Ratio | Strength |
|-------|----------|
| 1.5–3× | weak |
| 3–10× | moderate |
| 10× or more | strong |
| below 1 | counts against; use the same scale in reverse (1/3 is moderate against) |

Odds = p / (1 − p). New odds = old odds × ratio. Back to a probability: p = odds / (1 + odds).

- Count items from one original source only once.
- Items that share a cause or a method are not fully independent. Use smaller ratios for them.
- If a ratio is a rough guess, say so.
- Give the result as a range, not a single number.

Then name the new data or test that would move the result the most for the least cost. If a search can find it now, get it and run Steps 2–5 on it.

## Step 6: Independent judge

Spawn a fresh sub-agent as the judge. Give it only the hypothesis, the rival explanations, the prior, the item table, the noise list with failed tests, the cause ratings, and the odds update. Do not give it the chat, your notes, or your opinion. Send it this prompt with those items:

```
You are an independent judge. Check this signal-and-noise analysis for mistakes.
You have not seen how it was made. Trust nothing; check everything.
You may look up sources to verify them.

1. Sources: Is each kept item traced to its original source? Is any item counted
   twice under different sources? Is any number misquoted (date, unit, scope)?
2. Noise kept as signal: Is any kept item about as likely whether the hypothesis
   is true or false? Is any an opinion, a one-off spike, or a pattern found by
   searching many variables?
3. Signal dropped as noise: Was any item dropped by mistake, especially one that
   counts against the hypothesis?
4. Cause and effect: Is any "direct cause" or "likely cause" really only a
   correlation? Was a third factor, reverse cause, selection, chance, or
   regression to the mean missed?
5. Update: Are the prior and the ratios reasonable? Is the result more certain
   than the evidence allows?

Return each issue as: [item ID] MAJOR or MINOR: problem → fix.
MAJOR: the result depends on it.
MINOR: it does not change the result.
End with PASS (no MAJOR issues) or FAIL.

[analysis]
```

When the judge replies:

- **MAJOR issue:** go back to the step where the mistake started and redo the steps from there. Then run a new judge. Repeat until the judge finds no MAJOR issue.
- **MINOR issue:** fix it directly. No need to redo the steps.
- Add any issue you cannot fix to the open questions.

## Final output

If the `tldr` skill is available, read its SKILL.md and follow its writing rules: simple English, no invented terms, and short bullets. Ignore its section list. Use the sections below instead, and keep the tables as tables.

```
## Signal and Noise: [hypothesis]
**Hypothesis:** [cause → effect claim]
**Result:** [probability range now, the prior it started from, and the main reason it moved]
**Cause-effect evidence:** [items rated direct or likely cause: what each shows, and its source]
**Evidence against:** [items that count against the hypothesis]
**Open questions:** [missing data and the cheapest test that would move the result most;
issues the judge raised that could not be fixed]

### Appendix
**Items kept:** [ID | Claim | Original source | Rating | Ratio]
**Noise removed:** [ID | Claim | Failed test]
**Odds update:** [prior → each item's ratio → result]
```
