---
name: ml-scientist
description: Runs ML and deep-learning work as research. Observe the data, reason, state a hypothesis, test it with training and validation, find out why, and repeat.
when_to_use: Use it whenever a task trains or improves a machine-learning, deep-learning, or neural-network model. Use it even if the user does not name this skill. It covers building, tuning, debugging, comparing, and evaluating models. It covers classifiers, regressors, forecasting, ranking, recommendation, and fine-tuning. It covers tabular, time-series, vision, audio, text, and graph models. It covers choices of architecture, features, loss, data size, model size, validation, and metrics. It covers problems such as overfitting or weak accuracy. Trigger phrases are "train a model", "build a classifier", "predict", "forecast", and "improve accuracy". More trigger phrases are "which architecture", "neural network for", "deep learning model", and "why is my model overfitting". Do not use it for prompt-only work with a hosted LLM. Do not use it for data analysis with no model.
argument-hint: "[ML task or research question]"
---

# ML Scientist

Do ML work as research, not as trial and error. Every experiment tests a hypothesis. Every hypothesis comes from an observation and clear reasoning. Every result gets an explanation.

Task: $ARGUMENTS

If no task is given, the task is the ML work in the current conversation.

## How to start

- **The user called `/ml-scientist`:** run all phases in order.
- **The skill loaded during ML work:** find the phase that matches the current state of the work. Start there. Check earlier work against the rules, and fix what breaks them.
- **The user asks a narrow ML question:** this means one bug, one metric, or one choice in an ML, deep-learning, or neural-network model. Answer it. Apply the rules and phases that touch it. Do not use this skill for narrow questions about anything else.

You, the agent, do every phase yourself. Infer what you can from the request, code, and data. Write your assumptions down and keep going. Stop and ask only if a missing fact would change the experiment.

If the `first-principles` skill is available, read its SKILL.md. Use its Step 1 to find the real problem. Use its fact rules when you reason in Phases 2 and 3. Skip its judge step, because Phase 6 has its own judge.

## Rules

1. **No blind changes.** Experiment tests written hypothesis.
2. **Hypotheses come from observation and reasoning.** Each one starts from an observation in the data or in past results.
3. **A simple model is only a baseline.** Never stop there.
4. **A gain counts only if it is larger than the noise.**
5. **Hold back the test set.** Use it once, at the end.

## Phase 1: Define the real problem

Capture the context before you open the data. Write down:

- **Goal:** what the user wants to achieve, in plain words with no ML terms.
- **Action:** what someone will do with each prediction. A prediction that drives no action has no value.
- **ML fit:** check whether a simple rule or the current non-ML method is good enough. That method becomes the first baseline.
- **Claim type:** decide whether the goal is to predict or to explain cause and effect. A model that predicts well does not show cause.
- **Output and label:** the type of output the model must give. If the true target is not in the data, name the proxy label. Write how the proxy differs from the true target.
- **Inputs at prediction time:** the inputs that exist when the model must predict.
- **Success:** the success measure for the goal, and the model metric that tracks it. Check that a better model metric moves the success measure.
- **Target:** the score that counts as success. Ask whether even a perfect model would reach the goal.
- **Limits:** data, compute, time, cost, and speed limits.
- **Risks:** the main ways the project could fail.

Then do two things:

- **State the core of the problem** in one or two sentences: the output, the core difficulty, and the domain.
- **Fix the evaluation before the first run.** Fix the metric, the baselines, and the success and failure criteria. Do not change them after you see results. If a change is necessary, record why, and rerun the baselines.

## Phase 2: Observe the data

Study the data before any model. Look at real rows, not only summaries. Keep the raw data read-only.

**Meaning**

- Build a data dictionary: the meaning, units, valid range, source, and derivation of each field.
- Find the unit of observation and its structure: rows per entity, groups, sites, batches, time, and space.
- Check that the data is trusted, consistent over time, correctly labeled, and like the data at deployment.

**Quality**

- Find missing-value codes, and why values are missing. Keep "missing", "not measured", and a true zero apart.
- Find placeholder values, impossible values, broken business rules, and fields that contradict each other.
- Find duplicates on the natural key.
- Check labels by hand on a sample.
- Flag outliers, and measure their effect. Do not delete them by rule. Outliers often reveal bugs.

**Patterns**

- Compare means with medians. Note skew, long tails, and more than one peak. More than one peak can mean mixed populations.
- Plot values in time order to find trends, seasons, and drift.
- Check which features relate to the target. Note derived and redundant fields.
- Check that a pattern holds inside segments as well as overall. A trend can reverse when you split the data.
- Ask how you yourself would predict the target from these inputs. Your answer points to features and model structure.

**Splits**

- Find how the train, validation, and test sets were made. Find which unit, group, or time the split used.
- Check for overlap across the sets: exact duplicates, near duplicates, shared entities or groups, and overlapping time ranges.
- Decide whether the split should be random. Shuffle rows only if the rows are independent. Keep each group in one set. Split by time when the model predicts the future.
- Check the row order in each file. Sorted files give a biased first sample and correlated training batches.
- Check that the test set looks like the deployment data.
- Look for leakage: inputs not known at prediction time, and inputs built from the target. Treat any input that predicts almost perfectly as leakage until you prove otherwise.

**Record**

Write each observation as a fact with its evidence: a plot, a count, or a test. Write the observation before you interpret it. Mark patterns that you found by looking as exploratory.

Keep a journal. Read it before each step. Record surprises and hunches, because they lead to hypotheses.

## Phase 3: Form hypotheses

A hypothesis explains an observation with a mechanism. Write it as "X, because Y, so we predict Z." A hypothesis can be about the data, features, loss, architecture, data size, model size, or training.

1. **Start from an observation.** Use the journal, the data patterns, past results, and the worst errors.
2. **List rival explanations.** For each observation, list other possible causes: a data artifact, leakage, a hidden common cause, sample selection, or chance.
3. **Generate many ideas before you judge any.** Write at least 10. Work through these directions:
   - **Reframe the problem:** change the objective, the form of the output, the level of detail, or the direction of prediction.
   - **Drop a hidden assumption:** list what the current approach takes for granted. Ask what happens if it is false.
   - **Borrow from another field:** find a field that solves the same structure. Map its mechanism, not its labels.
   - **Combine or split:** join two methods, or split one method into parts and change one part.
   - **Test a trade-off:** check whether a trade-off is real, or only a limit of the current method.
   - **Check what changed:** retry an old idea if the limit that stopped it no longer applies.
   - **Find where it breaks:** find the conditions where the current model fails, and find why.
   - **Change the level:** solve a more general version of the problem, or a narrower one.
4. **Write a hypothesis record** for each serious idea
5. **Prefer tests that separate rivals.** Choose a test where the hypothesis and its rivals predict different results.
6. **Filter.** Drop ideas that you cannot state in two sentences. Drop ideas that a simpler approach already solves. Drop ideas that do not fit the limits.
7. **Rank** by chance of success, size of gain, and cost. The first round must include at least two ideas beyond the baseline and the standard model.

## Phase 4: Test, analyze, repeat

**Before the first real run**

- Run the full pipeline end to end on a small, representative subset. Use the same model type and metric as the full run.
- Check the setup before you trust results: the baseline reproduces, training converges, and the data loads correctly.
- Measure the noise. Rerun the unchanged baseline a few times.
- Keep the first runs short. Scale up only the ideas that pass the small test.

**For each hypothesis**

1. Write the test plan before the run, and commit it.
2. Change only what the hypothesis names.
3. Give a new idea the same tuning budget as its control.
4. Log what the success criteria need, plus a few diagnostics that could explain a surprise.
5. Compare the result with the prediction. Mark it supported, refuted, or unclear. A gain inside the noise is unclear, so rerun it.
6. Find out why, with evidence: errors by segment, training curves, or a run with one part removed.
7. Keep a change only if it beats the current best by more than the noise. Also keep a change that ties with simpler code.
8. Record the result, the reason, and the next idea, even when you discard the change. This record is the only memory that survives a revert.
9. Turn each explanation into a new observation, and go back to Phase 3.

Experiment log columns:

```
id | hypothesis | prediction | result (metric, 95% CI) | verdict | why | next
```

**Every 5-10 runs, review**

- Group the results by the kind of change. Find which kinds worked and which did not.
- Name the mechanism behind each win and each loss.
- Update the findings: what we know, what explains it, what not to repeat, and what is still open.
- Choose one direction:
  - **Go deeper:** push further on the mechanism that worked.
  - **Broaden:** test the next hypothesis on the list.
  - **Change course:** a key assumption failed. Go back to Phase 1 or 2.
  - **Stop:** the target is met, or no hypothesis with a fair chance is left.

**When progress stops**

- After three tries of the same mechanism, choose a different kind of hypothesis.
- Study the data, the errors, and the code again before you try new settings.
- Do not sweep hyperparameters without a hypothesis. Do not keep a change that you cannot explain.
- If results surprise you, or no ideas are left, search for the SotA.

### Optional: search for the SotA

Do this step only when the loop stops making progress. SotA means the best published method for this exact problem today.

1. Start from the core problem statement in Phase 1. Find the research areas that study the same problem. They share the kind of output and the core difficulty. Their domain is the same or close.
2. Search those areas for the best current methods. Prefer recent work. Do not search wide on loosely related topics. Do not rely on surface keywords from the task.
3. For the top methods, read the paper and the code, not a summary. Note the key mechanism, the evidence, and the test conditions.
4. Record what you searched. If you find nothing, write "not found in this search", not "no prior work exists".
5. Write a table with one row per method: method, key idea, evidence, test conditions, and fit to our data. Note what the methods still fail at, and how our data differs.

Treat each finding as a new observation. Go back to Phase 3.

## Phase 5: Test honestly

- **Same split rules in every fold:** apply the Phase 2 split rules to tuning folds too.
- **Folds:** fit every transform, feature selection, and resampling step inside the training fold only.
- **Holdout or CV:** use cross-validation (CV) if the test set's 95% CI is wider than the gain that matters. If you tune and report from CV on small data, use nested CV. If data is large and training is costly, use one train, validation, and test split.
- **Permutation test:** use it when data is small or features are many. Also use it when the signal is weak, the search was large, or chance level is unclear.
- **Noise and units:** compare models on the same folds or rows. Count seeds, folds, or groups as the units, not rows, unless rows are independent. For deep learning, run 3 or more seeds.
- **Many comparisons:** the best of many models on one validation set is partly luck. Confirm the winner on held-out data.
- **Reproducibility:** set seeds. Record the configuration, data version, and environment of each run.
- **Scorecard:** use the same columns for every run. Give the primary metric with its 95% CI and the number of runs. Give the gain over the best baseline, and the train-validation gap. Also give the other metrics, the permutation p-value if you ran the test, and the cost.

## Phase 6: Final test and independent judge

1. Retrain the chosen model on the training and validation data. Score the held-back test set once. Do not tune again.
2. Start a new sub-agent as the judge. Give it only the Phase 1 notes, the observations, the hypothesis records, the experiment log, and the scorecard. Also give it the SotA table, if you made one. Send it this prompt with those items:

```
You are an independent ML research reviewer. You did not see how this work
was done. Trust nothing; check everything. You may search to verify.
1. Problem: Does the metric track the real goal? Were the evaluation rules
   fixed before the results?
2. Data: Were the splits checked for overlap, order, and match to deployment?
   Was leakage checked?
3. Reasoning: Does each hypothesis come from a real observation? Were rival
   explanations considered? Were any changes made without a hypothesis?
4. Analysis: Does each verdict match its evidence? Is each "why" supported?
   Were refuted hypotheses reported honestly?
5. Beyond the baseline: Were new ideas really tested with a fair budget?
6. Test use: Was the test set used more than once, or used for any choice?
7. Evidence: Does each claimed gain beat the best baseline by more than the
   noise? Were the units of comparison independent?
Return each issue as: [item] MAJOR or MINOR: problem → fix.
MAJOR: the result or a claim depends on it. MINOR: it does not.
End with PASS (no MAJOR issues) or FAIL.

[items]
```

- **MAJOR issue:** go back to the earliest phase it affects. Redo the work from there. Run a new judge.
- **MINOR issue:** fix it directly.
- **Issue you cannot fix:** list it under Risks.

## Final output

If the `tldr` skill is available, read its SKILL.md. Follow its writing rules: simple English, no invented terms, and short bullets. Ignore its section list. Use the sections below instead, and keep the tables as tables.

```
## ML Result: [task]
**Goal:** [goal, success measure, primary metric, target]
**Result:** [test scorecard and the plain-words sentence]
**Chosen model:** [what it is, and the supported hypothesis that explains why it works]
**Hypotheses tested:** [supported or refuted, and what each one showed]
**Compared with SotA:** [only if you searched: where the model stands against the best published methods]
**Risks:** [judge issues not fixed, data shift, weak segments, assumptions]
**Next step:** [the next hypothesis to test, and the observation behind it]

### Appendix
Observations · Hypothesis records · Experiment log · SotA table (if made)
```
