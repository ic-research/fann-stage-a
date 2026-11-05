# 5. Baselines

**Rule.** Run every baseline on the same hardware as the system. Use the same software. Use the same test stream.

**Explanation.** A baseline is a comparison system, not a second research result. If the hardware or the stream differs, a cost difference does not answer the question.

**Rule.** If you cannot build a baseline fairly, remove it. Write the reason.

**Rule.** The best baseline for a workload is the lowest-cost baseline that meets the quality tolerance on the risk-control split.

**Explanation.** You select the best baseline on calibration data. You do not select it on the test stream. A baseline that is cheap and outside the tolerance is not the best baseline.

| ID | Baseline | What you build | What a win against it shows |
|----|----------|----------------|-----------------------------|
| B0 | Always full | The reference model runs on every input. | Nothing about a saving. B0 is the quality reference and the cost reference. |
| B1 | Exact cache | Store the output under a hash of the input. Return that output when the hash matches. | Gate 0 only. An exact cache does not hit a near-repeat. |
| B2 | Semantic cache | Embed the input with a separate public model. Return the stored output of the nearest earlier input when its cosine similarity is at or above a threshold. Use the same store size and the same FIFO rule as the gate. | The reuse route adds something that a similarity cache does not already give. |
| B3 | Confidence cascade | Run the light path on every input. Take its top-class probability after temperature scaling. If the probability is at or above a threshold, return the light label. If not, run the reference model and return its label. | Familiarity adds something that confidence does not add. |
| B3b | Competence gate | A small network reads the same depth-`k` signature as the system and predicts whether the light path will give the B0 label. If the prediction is at or above a threshold, run the light path. If not, run the rest of the reference model. | Stored history adds something that a trained predictor without history does not add. |
| B4 | Always light | The light path runs on every input. | The light route is not only "use a smaller model for every input." |

## B2, semantic cache

**Rule.** The B2 embedding model is not the reference model, not the light path, and not the slice embedding in [06-data.md](06-data.md).

| Workload | B2 embedding | Revision | Licence |
|----------|--------------|----------|---------|
| W2 | `facebook/dinov2-small`, CLS token, L2-normalized | `ed25f3a31f01632728cabb09d1542f84ab7b0056` | Apache-2.0 |
| W3 | `sentence-transformers/all-mpnet-base-v2`, mean pool, L2-normalized | `e8c3b32edf5434bc2275fc9bab85f82640a19130` | Apache-2.0 |

**Explanation.** B2 uses a different model to embed the input. The gate uses a signature from the reference model. That difference is part of the comparison. Give B2 the same store size. If you do not, a larger memory can look like a better method. B2 pays the cost of its embedding model on every input.

## B3, confidence cascade

**Rule.** Fit the temperature `T` on the fit split only. Minimize the negative log-likelihood of the gold label. Optimize `log T`, start at `T = 1`, and use L-BFGS. Stop when the gradient is below `10^-6` or after 50 steps.

**Rule.** The B3 score is the maximum class probability of the light path after division of its logits by `T`. The label of a light decision is the unscaled argmax.

**Rule.** Declare nine threshold candidates before calibration: 0 (always light), "never light", and the 0.50, 0.60, 0.70, 0.80, 0.90, 0.95, and 0.99 quantiles of the score on the fit split.

**Explanation.** Temperature scaling is a calibration method. It adjusts the confidence score so that the score matches the rate of correct outputs. B3 without calibration can look strong because its score is not a probability. `T` does not change the argmax. The cascade form uses the light path's own confidence, so the score belongs to the model whose label it returns. B3 pays the light-path cost on every input, and the reference cost on every deferred input.

## B3b, competence gate

**Rule.** The B3b input is the system's signature at depth `k` from [04-models.md](04-models.md). Train one B3b gate for each `k`.

**Rule.** The B3b network is a two-layer MLP: a linear layer to 128 units, GELU, and a linear layer to one logit.

**Rule.** The training target for an input is 1 when the light-path label equals the B0 label, and 0 when it does not. Train with binary cross-entropy on the fit split only. Use AdamW, learning rate `10^-3`, batch size 256, and 20 epochs. Use seed `20261030`. Do not use the risk-control split or the test stream to train, to stop early, or to select an epoch.

**Rule.** Declare nine threshold candidates for each `k`, in the same form as B3: "never light", always light, and the 0.50, 0.60, 0.70, 0.80, 0.90, 0.95, and 0.99 quantiles of the B3b probability on the fit split.

**Explanation.** B3b is rung 2 of a ladder: confidence gate, competence gate, prototypes or compressed memory, full pass history. Stage A tests rungs 1, 2, and 4. B3b reads the same signature as the system and decides between the same light and full routes. The difference is that B3b keeps what it learned in weights, fixed after training, and the system keeps a store of past passes. A win of the system against B3b is evidence that stored history carries information that a trained predictor without history does not. Trained gates of this kind are published work (for example CALM and ConfidNet). B3b is a baseline, not a claim.

**Explanation.** B3b pays the reference prefix up to depth `k` and its own small network on every input. On a light decision it then pays the light path. On a full decision it pays the rest of the reference model.

## B4, always light

**Explanation.** B4 always pays the light-path cost. The gate pays the full cost, the light cost, or the reuse cost, and it also pays the gate cost. If B4 meets the tolerance at a lower cost, B4 is the best baseline, and the system must beat B4. [04-models.md](04-models.md) requires a light path that does not already meet the bound as "always light".

## Removed at the freeze: B5, temporal delta

**Explanation.** B5 was a published frame-delta method, such as Eventful Transformers or DeltaCNN. It was removed before the freeze, for two reasons. First, it was cut to save time. Second, W2 is now per-image classification of camera-trap bursts, not continuous video, so a method that recomputes only the change from the previous frame has no continuous frame sequence to use. The report says that W2 is compared with the caches, the confidence cascade, and the competence gate, and not with a frame-delta method.
