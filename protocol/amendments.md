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

Date: 11 Nov 2026
Protocol version after this amendment: v0.4.1
What changed: Added one extra experiment, X1, for W3 only. X1 is a small model for the BANKING77 task, built on the distil labs platform. It is scored as an extra always-light comparison on the same W3 test stream, with the same metrics and the same cost accounting as B4. X1 is not the W3 light path. It does not replace the fine-tuned DistilBERT or any baseline. It does not count toward Gate 1 or Gate 2.
X1 follows these rules. (1) X1 trains only on the W3 model-training split. It never sees the fit split, the risk-control split, or the test stream. (2) X1 is built once. Record the base model, the platform version and date, every setting that was chosen, and the number of builds. Fix them before any code reads the W3 test stream. Do not choose any setting with the test stream. (3) Record the name, version, and weight hash of X1 in the manifest before any code reads the W3 test stream, as 04-models.md requires for every model. If the weights cannot be downloaded, score X1 through the platform's interface on the W3 test stream, report quality only, and say that its cost cannot be compared and that no weight hash is available. (4) Run X1 as an always-light policy on the risk-control split. If its one-sided upper bound on disagreement with B0 is at most alpha, report that X1 meets the error bound on its own. This does not change the W3 light path. (5) Report X1 in every case, including if it matches or beats the system. (6) distil labs provided platform credits, $200 at sign-up. Name the credits in the report. distil labs has no say in the design, the analysis, or whether X1 is reported.
Why it changed: distil labs offered credits to build a small model for this task. The check shows how strong a small W3 model can get, so a reader can judge whether B4, the always-light baseline, is a weak one. The experiment is added before any run on the W3 test stream, and X1 is reported whichever way it comes out.
Files: protocol/amendments.md; protocol/README.md

Date: 14 Nov 2026
Protocol version after this amendment: v0.4.2
What changed: The W3 fine-tuning recipe in 04-models.md changes from learning rate `2 × 10^-5` and 4 epochs to learning rate `5 × 10^-5` and 10 epochs. Every other item stays as written: AdamW, weight decay 0.01, linear decay to 0, no warm-up, batch size 32, maximum length 64, seed `20261030`, the splits in 06-data.md, and the two base revisions. The rule "fine-tune once" now means once with this recipe, for both models, in one run.
The first run used the earlier recipe and failed the quality bar. It is a recorded failed run and is not used for anything else. On the risk-control split the reference model scored 0.6384 against gold, and the bar is 0.85. The light path scored 0.6904. Disagreement between the two was 30.56% (765 of 2,503). Weight hashes: reference `c0f1f521edd89f2b4218f9888d4ff22d7ae9ae292a333601f24fbcf560e88e07`, light `f83aa57cef3604f43f646c844d3da84aa23b00e0d9cc01f15125ad7b7f32cacf`. The weights stay on the `fann-stage-a` Volume under `w3/failed-v0.4.1`. No threshold, no baseline selection, and no test-stream read used these models.
The new recipe follows these rules. (1) It was chosen with the model-training split only. A diagnostic held out 500 of the 5,000 queries, trained on the other 4,500, and compared six recipes on the held-out 500. The frozen recipe scored 0.638 for BERT-base and 0.650 for DistilBERT. Learning rate `5 × 10^-5` with 10 epochs scored 0.900 and 0.910. The diagnostic never read the fit split, the risk-control split, or the test stream. (2) The risk-control split was read once before this change, by the failed run's quality check. It was not used to choose the new recipe. (3) The quality bar of 0.85 and the light-path rule in 04-models.md do not change. (4) The new run is the only run with the new recipe. If the reference model fails the bar again, report W3 as not meeting the quality bar. Do not train a third time without a further amendment. (5) Record the new weight hashes in the manifest before any code reads the W3 test stream.
Why it changed: The reference model failed the quality bar. The failed run stopped at a training loss of about 2.5, close to the loss of a random guess over 77 classes, which is 4.34. The diagnostic showed the same accuracy of about 0.64 on held-out model-training queries, and showed that a higher learning rate or more epochs fixes it. The data, the splits, the label handling, and the training code were checked and do not explain the failure. The bar was set before any model was run and stays at 0.85.
Files: protocol/amendments.md; protocol/README.md

Date: 14 Nov 2026
Protocol version after this amendment: v0.4.3
What changed: Filled the three W3 items under "Filled after the freeze" in 10-open-items.md: fine-tune the two models and write the weight hashes, measure the reference accuracy against the 0.85 bar, and run the always-light check. No rule changes. This amendment fills the two `[ ]` weight-hash slots of the W3 rows in the table in 04-models.md. Those slots read as written below, and the table is not edited in place.
The run followed the v0.4.2 amendment: one run, both models, learning rate `5 × 10^-5`, 10 epochs, and every other item of the recipe as in 04-models.md. It was made with the `quotient` repository at commit `085f235`, on one NVIDIA A10, with torch 2.6.0, transformers 4.48.3, and numpy 1.26.4. The results are on the `fann-stage-a` Modal Volume under `w3/final`.
Weight hashes (sha256 of `model.safetensors`). W3 reference, BERT-base, base revision `86b5e0934494bd15c9632b12f734a8a67f723594`: `4b97d363ba36242a37474cc19ca9adfef355c989a51489000a0e42182e603501`. W3 light path, DistilBERT, base revision `12040accade4e8a0f71eabdb258fecc2e7e948be`: `8d3966629d225d0564853ff45d21c5d7596fe9570989a3a7053250e8bd856e67`.
Inputs. Train CSV sha256 `b06e26ac675513959a63135f11b94ea7786ed02da65db93a5650d8838cbc664b`, checked against the Hugging Face `PolyAI/banking77` train split with no mismatch in text or label over 10,003 rows. The split indices are in `w3/splits.json` on the Volume, written before any model ran: file sha256 `ea495d9381442365c4f43ce347f8ab14fb37005c681b324956d9f33f134b4b0c`, split digest `2588fce035ff155a9a67e9ffcb913916e850fb8c2240dbaceb138dae74394bb2`.
Quality bar, on the risk-control split of 2,503 queries. The reference model scored 0.9033 against the gold label. The bar is 0.85. The model passes. The light path scored 0.9073.
Always-light check. The light path and the reference model disagree on 145 of 2,503 queries, a rate of 0.0579. The one-sided Clopper-Pearson upper bound at significance 0.05 is 0.0662. This is above `α = 0.01`, so the light path is not too strong and stays as the W3 light path.
Why it changed: 10-open-items.md requires these values as dated amendments, from calibration data only, before any code reads the W3 test stream. The test stream has not been read. The code that ran has no path to it. The earlier failed run and its hashes are in the v0.4.2 amendment.
Files: protocol/amendments.md; protocol/README.md
