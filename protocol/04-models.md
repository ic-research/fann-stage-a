# 4. Models

**Rule.** Use one reference model for each workload. The model must have open weights. Select the smallest model that reaches acceptable quality.

**Rule.** Record the name, the version, and the weight hash of each model. Load the weights by that hash. Refuse a run if the hash changes.

**Explanation.** The weight hash is a short code for the exact file. The code changes if the file changes. The hash lets another person run the same weights.

**Rule.** Use one light path for each workload. The light path is a model for the same task that costs less than the reference model. Build it from the reference model by quantization, distillation, or pruning, or use a smaller distilled, quantized, or pruned model for the same task.

**Explanation.** The light path is a tool. The test result is the route that the gate selects. Do not report the light path as the research result.

**Rule.** The light path must not already meet the error bound on every input. Run the always-light policy on the risk-control split. If its one-sided upper bound on disagreement with B0 is at most `α`, the light path is too strong. Select a weaker light path, on calibration data only.

**Explanation.** If the light path agrees with the reference almost always, every gate has nothing to decide, and the cheapest system is "always light". The test then says nothing about familiarity.

**Rule.** A model must not have seen the calibration data or the test stream, unless both come from the same random split.

**Explanation.** The published BANKING77 models were fine-tuned on the train set. On the train set their accuracy is 99.96% and 98.18%. Their own cards report about 93.6% and 92.5% on the test set. Thresholds from data that a model has seen do not hold on data that it has not seen. W2 does not have this problem, because its calibration data and test stream are a random split of one set.

**Rule.** W3: fine-tune the reference model and the light path once, before calibration, on the model-training split in [06-data.md](06-data.md). Use the recipe below for both. Then do not change either model.

**Rule.** W2: use the reference model as published.

**Rule.** Do not train a reference model or a light path after calibration starts. You may train the B3b gate. [05-baselines.md](05-baselines.md) defines B3b.

## W3 fine-tuning recipe

| Item | Value |
|------|-------|
| Optimizer | AdamW, learning rate `2 × 10^-5`, weight decay 0.01 |
| Schedule | Linear decay to 0, no warm-up |
| Batch size | 32 |
| Epochs | 4, fixed |
| Maximum length | 64 tokens |
| Seed | `20261030` |

**Rule.** Use every epoch. Do not stop early. Do not select a checkpoint. Do not use the fit split, the risk-control split, or the test stream during fine-tuning.

## Selection

| Workload | Role | Model | Version and hash | Licence |
|----------|------|-------|------------------|---------|
| W3 | Reference (B0) | BERT-base, 12 layers, fine-tuned on the model-training split. Base: `google-bert/bert-base-uncased`. | Base revision `86b5e0934494bd15c9632b12f734a8a67f723594`. Fine-tuned weight hash `[ ]`. | Apache-2.0 |
| W3 | Light path | DistilBERT, 6 layers, fine-tuned on the model-training split. Base: `distilbert/distilbert-base-uncased`. | Base revision `12040accade4e8a0f71eabdb258fecc2e7e948be`. Fine-tuned weight hash `[ ]`. | Apache-2.0 |
| W2 | Reference (B0) | SpeciesNet v4.0.3b, the whole-image classifier (Google, EfficientNet V2 M, PyTorch), from Kaggle `google/speciesnet`. | Version `v4.0.3b`. Weight-file hash `[ ]`, written at download. | Apache-2.0 (code and every weight version on Kaggle) |
| W2 | Light path | `[ ]` Built from SpeciesNet v4.0.3b by quantization or pruning. Selected on the calibration splits only. | `[ ]` | Apache-2.0, as the reference |

**Rule.** Both models of a workload must emit the same label names. Compare labels by name. Never compare by raw class index. If the name sets differ, stop. Do not build a guessed mapping.

## Quality bar

**Rule.** W3: the reference model is acceptable when its accuracy against the gold label on the risk-control split is at least 0.85. Measure this after fine-tuning.

**Explanation.** The bar is 0.85 and not 0.90 because the W3 models learn from 5,000 queries, about half of the official train set. The bar was set before any fine-tuned model was run.

**Rule.** W2: write the bar and the measured accuracy against the gold label on the calibration splits. Map SpeciesNet labels to the CCT-20 classes with a written table. Use that table only for the accuracy against the gold label. Routing and the false-familiar rate use the SpeciesNet label names.

**Open item.** The W2 quality bar and the W2 label-mapping table.

## Gate depths

**Rule.** W3: the gate depths `k` are the outputs of encoder layers 3, 6, and 9 of the reference model. These are 25%, 50%, and 75% of 12 layers.

**Rule.** W2: the gate depths `k` are the outputs of the blocks at 25%, 50%, and 75% of the block count of the reference model. Round down to a whole block. Write the block indices.

**Rule.** W3: the signature is the mean of the hidden states at depth `k` over the non-padding tokens, L2-normalized. The dimension is 768. Do not reduce it.

**Rule.** W2: the signature is the global average pool of the feature map at depth `k`, L2-normalized, at the native channel count. Do not reduce it.

**Explanation.** A reduction adds a fitted transform and a choice. The native dimension avoids both. The lookup cost at the native dimension is part of the gate cost that [07-metrics.md](07-metrics.md) measures.

## Release

**Rule.** W3: release the two fine-tuned models, with the fine-tuning recipe. Anyone can then check the hashes and repeat the run.

**Rule.** W2: name the reference model by name, version, and hash. Do not redistribute its weights. Release the light-path recipe and configuration. Release light-path weights only when the upstream licence permits it.
