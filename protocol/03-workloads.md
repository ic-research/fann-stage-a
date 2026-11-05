# 3. Workloads

**Rule.** Use two public workloads in stage A: W2, camera frames, and W3, language.

**Rule.** W1, documents, is not in stage A. Do not run W1 for a stage A result.

**Explanation.** UA-DETRAC, the first video candidate, is not selected. Its licence is CC BY-NC-SA 3.0, which forbids commercial use.

**Explanation.** No document set met all three properties below by 30 Oct 2026. SROIE has 973 receipts, and its licence is not stated in any source that we found. CORD has a CC BY 4.0 licence, but its released set has 1,000 receipts, with 100 for test. That is too few routed inputs for a 1% bound with 95% confidence. W1 reuse is also limited to exact duplicates, so the reuse route would have almost nothing to do. W1 moves to a later stage. The reuse rule for documents in [02-system.md](02-system.md) stays, so a later stage does not have to write it again.

**Rule.** Each workload must have all three of these properties:

- an open licence that permits use by a company
- a task with a clear quality tolerance
- enough natural near-repeats to test the question in [01-question.md](01-question.md)

**Rule.** Measure the natural near-repeat rate before you select a workload. Use calibration data only. Do not use the test stream. Write the rate in this file.

**Rule.** Measure the rate with an independent embedding model. The model is not the reference model, not the light path, and not the B2 embedding. Order the inputs as the stream orders them. Use a stream with the same length as the test stream. An input is a near-repeat at cutoff `c` when an earlier input in that order has cosine similarity of at least `c`. Report the rate at the cutoffs 0.70, 0.80, 0.85, 0.90, and 0.95.

**Rule.** A workload qualifies when its natural near-repeat rate at cutoff 0.85 is at least 30%. If the rate is lower, reject the workload. Write the rejection in this file. Write the reason.

**Explanation.** A near-repeat is close to an earlier input. It is not the same input. The test needs near-repeats because an exact copy is a job for an exact cache. The question is about inputs that are only close.

**Explanation.** The stream length matters because a short stream has less history. On BANKING77 the rate at cutoff 0.90 is 41.9% over 10,003 queries and 23.4% over 3,080 queries. A rate from a longer stream overstates the rate of the test.

**Explanation.** You measure the rate before the selection. You do not select a workload and then discover that it has almost no near-repeats. Do not use the gate's own score to decide that a workload is suitable. The slices in [06-data.md](06-data.md) use the same separation.

## Selection

| ID | Type | Dataset | Licence | Size | Task and tolerance | Natural near-repeat rate |
|----|------|---------|---------|------|--------------------|--------------------------|
| W2 | Camera frames | Caltech Camera Traps, CCT-20 subset (Beery et al., 2018), from LILA BC | CDLA-Permissive 1.0 | 57,868 images from 20 fixed camera locations | Whole-image species classification. The label must be the same label that B0 gives. | `[ ]` Measure on the calibration splits, with the method above. |
| W3 | Language | BANKING77 (PolyAI), Hugging Face `PolyAI/banking77` | CC BY 4.0 | 10,003 train queries, 3,080 test queries | 77-way intent classification. The label must be the same label that B0 gives. | Measured on 30 Oct 2026, train split only. See below. |

**Explanation.** The tolerance on both workloads is exact: the same label as B0. Neither workload needs `ε`.

## W2, camera frames

**Explanation.** W2 uses camera traps. A camera trap does not move. A motion trigger takes a burst of images, and many images are close to earlier images from the same camera. Many images show no animal. The task is to classify the species in the whole image.

**Rule.** On W2, the reuse route is eligible for every image.

**Rule.** On W2, a reuse result is correct only when the reused label is the label that B0 gives for the new image. The false-familiar rate counts each reuse result with a different label.

**Explanation.** Eligibility is a check that the gate can do before it routes the input. Correctness is a check that you do after the run, against B0. The gate cannot know the label of a new image before it routes the image. The false-familiar rate measures the reuse results where the label changed.

**Explanation.** SpeciesNet, the W2 reference model in [04-models.md](04-models.md), was trained on more than 65 million images that include public repositories. It can have seen CCT-20 images. That affects accuracy against the gold label, which is a secondary metric. It does not affect the primary metrics, because those compare each system with B0.

**Explanation.** Use the images as the dataset releases them. Do not try to identify people.

**Open item.** Measure the natural near-repeat rate of W2 on the calibration splits, with the independent image embedding in [06-data.md](06-data.md). Write it in the table above.

## W3, language

**Explanation.** W3 is classification of banking queries. Two queries can use different words and can still require the same label. That pair is a near-repeat. Two queries can look close and can require different labels. That pair is a false-familiar error if the gate does not use the full route.

**Rule.** On W3, the reuse route is eligible for every query.

**Rule.** On W3, a reuse result is correct only when the reused label is the label that B0 gives for the new query. The false-familiar rate counts each reuse result with a different label.

**Explanation.** The gate cannot know the label of a new query before it routes the query. The label condition is therefore the correctness check, not the eligibility check.

**Measured rate.** We measured the natural near-repeat rate on the BANKING77 train split. We used no test query. The stream order was one shuffle with seed `20261030`. The embedding was `sentence-transformers/all-MiniLM-L6-v2`, mean-pooled and L2-normalized. The first 3,080 queries of that stream match the test-stream length.

| Cutoff | Rate, first 3,080 queries | Same gold label as the neighbour | Rate, all 10,003 queries | Same gold label as the neighbour |
|--------|---------------------------|----------------------------------|--------------------------|----------------------------------|
| 0.80 | 60.6% | 93.2% | 77.4% | 93.3% |
| **0.85** | **43.4%** | **95.9%** | 62.6% | 95.5% |
| 0.90 | 23.4% | 97.6% | 41.9% | 97.2% |
| 0.95 | 8.0% | 98.4% | 17.5% | 98.4% |

Exact duplicates, case-insensitive: 4 of 10,003 (0.04%).

**Explanation.** W3 qualifies: 43.4% at cutoff 0.85 is above the 30% floor. These queries are train queries, so the rate of the test stream can differ. The report gives the rate of the test stream. Exact duplicates are almost absent, so B1 and Gate 0 test very little on W3. Even at cutoff 0.95, 1.6% of near-repeats have a different gold label. That is above `α`. A reuse route can therefore pass the 1% bound only on very close matches.

**Explanation.** The light path alone (B4) can beat every other system on W3. If that happens, you report it. You do not drop the result.

## W1, documents (later stage)

**Explanation.** W1 is field extraction on documents that share a layout. A receipt from the same vendor can look like an earlier receipt. The date, the amounts, and the line items can still be new. Those values are the information. The tolerance is exact: a field is correct only when it matches the correct field value.

**Explanation.** [02-system.md](02-system.md) gives the reuse rule for W1 (v0.2). The reuse route applies to exact duplicates only. A near-repeat goes to the light route or to the full route. A gate that returns the old total because the layout is known is the error that W1 exists to catch. A later stage selects the W1 dataset.
