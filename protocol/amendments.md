# Amendments

**Rule.** Before the freeze, do not add an amendment. Change the draft in the file that owns the rule.

**Rule.** After the freeze, do not change a rule in the place where it stands. Add the change here.

Each amendment uses this block:

```text
Date:
Protocol version after this amendment:
What changed:
Why it changed:
Files:
```

## Change log before the freeze

**Explanation.** These entries record changes to the draft before the freeze. They are not amendments. Each change is already in the file that owns the rule.

Date: 21 Oct 2026
Protocol version after this amendment: v0.2
What changed: Fixed the familiarity signature and cosine-distance definition; gate depths; store sizes and FIFO eviction; restricted history entries to full-route outputs; specified workload reuse eligibility; set the 1% false-familiar target; and added an independent risk-control split with family-wise confidence correction. Added a conservative all-full fallback when calibration cannot support a safe threshold.
Why it changed: The v0.1 protocol left these implementation and error-control choices open. The amendment makes the gate reproducible, prevents reuse from propagating an approximate output, and prevents test-set tuning or unsupported low-risk claims.
Files: protocol/02-system.md; protocol/07-metrics.md

Date: 30 Oct 2026
Protocol version after this change: v0.3
What changed: Stage A uses two workloads: W2, Caltech Camera Traps (CCT-20), and W3, BANKING77. W1 moves to a later stage. Both tasks are classification with the B0 label as the tolerance, so `ε` is removed. Selected the W3 reference model and light path, and the W2 reference model (SpeciesNet v4.0.3b). The light-path rule now allows a published distilled, quantized, or pruned model for the same task, with a check that the light path does not already meet the bound on every input. Added B3b, the competence gate, on the same signature as the system. B4 is now "always light". B5 is removed. Gate 1 needs both workloads, and Gate 2 compares with the stronger of B3 and B3b. Wrote the split table, the slice embeddings and cutoffs, the repetition rates, batch size 32, the seeds, and the cost-accounting rules. The error control is now one family per workload and system, with Bonferroni for one threshold and fixed-sequence testing for the system's two thresholds. Measured the W3 natural near-repeat rate on the train split.
Why it changed: No document set met the licence and size properties. The 28 Oct decision added B3b. Our implementation plans supplied the concrete splits, candidates, and cost model. The v0.2 correction across all workloads would have needed more calibration inputs than the workloads have.
Files: protocol/03-workloads.md; protocol/04-models.md; protocol/05-baselines.md; protocol/06-data.md; protocol/07-metrics.md; protocol/08-gates.md; protocol/09-compute.md; protocol/10-open-items.md; protocol/terms.md; protocol/README.md

Date: 5 Nov 2026
Protocol version after this change: v0.4
What changed: W3 no longer uses the published BANKING77 checkpoints. It fine-tunes BERT-base and DistilBERT once on a new 5,000-query model-training split, with a fixed recipe. The W3 fit split is now 2,500 queries and the risk-control split is 2,503 queries. The near-repeat floor is now 30% at cutoff 0.85, measured on a stream of test-stream length. W3 store sizes are 1,000 and all records, so W3 has 6 cells and B2 has 16 candidates on W3. The light-path rule no longer needs a published model. Listed items may now be filled after the freeze as dated amendments, from calibration data only, before the workload's test stream is read. The freeze date moved to 5 Nov 2026. The four 30 Oct choices were confirmed on 5 Nov: W1 out of stage A, W2 as CCT-20, a smaller same-family light path with B4 as always light, and the per-workload error control. UA-DETRAC's rejection reason is corrected to its non-commercial licence. The W3 quality bar is 0.85, because the W3 models learn from about half of the train set. The 20% cost-saving bar and the 1% quality-loss cap are confirmed. The decision date is 12 Dec 2026. The GPU type and price move to the items filled after the freeze. The two W3 fine-tuned models are released with the recipe, so anyone can repeat the run. Notes that pointed to internal plans became rules or explanations in this text, or were removed.
Why it changed: A pre-freeze check showed 99.96% and 98.18% accuracy on the train set, against about 93.6% and 92.5% on the test set in the model cards. The published models had seen the planned calibration data, so a bound from that data would not hold on the test stream. The near-repeat rate at cutoff 0.90 falls from 41.9% over 10,003 queries to 23.4% over 3,080 queries, which is the test-stream length. A W3 store cannot reach 10,000 records.
Files: protocol/README.md; protocol/01-question.md; protocol/02-system.md; protocol/03-workloads.md; protocol/04-models.md; protocol/06-data.md; protocol/07-metrics.md; protocol/09-compute.md; protocol/10-open-items.md; protocol/terms.md

## Amendments after the freeze

None.
