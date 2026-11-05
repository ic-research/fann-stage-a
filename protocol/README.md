# Stage A protocol

Version: 0.4.

Status: frozen.

Freeze date: 5 Nov 2026. The freeze timestamp is the date and time of the commit that added this text.

Amendments after the freeze: none. The changes before the freeze are in the change log in [amendments.md](amendments.md).

This folder is the stage A protocol. The protocol gives the rules for the first test. Read the files in the order below.

[08-gates.md](08-gates.md) gives the result that the test must show. [terms.md](terms.md) gives the words that the protocol uses. Use these words. Do not use a second word for the same thing.

The sentences follow ASD-STE100. Each sentence has one subject and one action. Each thing has one name.

## How to read each file

Each file has three kinds of text.

- **Rule.** A requirement. You freeze the rules.
- **Explanation.** The meaning of a rule. An explanation is not a new requirement.
- **Open item.** A value that you must write before the freeze. An empty value is `[ ]`.

## Reading order

1. [terms.md](terms.md) gives the words.
2. [01-question.md](01-question.md) gives the question that the test answers.
3. [02-system.md](02-system.md) gives the gate and the three routes.
4. [03-workloads.md](03-workloads.md) gives the two workloads.
5. [04-models.md](04-models.md) gives the reference model and the light path.
6. [05-baselines.md](05-baselines.md) gives the comparisons.
7. [06-data.md](06-data.md) gives the splits, the test stream, and the slices.
8. [07-metrics.md](07-metrics.md) gives quality, the false-familiar rate, and cost.
9. [08-gates.md](08-gates.md) gives the pass rules, the kill rule, and the report.
10. [09-compute.md](09-compute.md) gives the two compute tiers.
11. [10-open-items.md](10-open-items.md) gives every value that is still empty.
12. [amendments.md](amendments.md) gives the changes after the freeze.

## Freeze

**Rule.** Fill every open item before the freeze, except the items under "Filled after the freeze" in [10-open-items.md](10-open-items.md).

**Rule.** Fill each of those items as a dated amendment. Use calibration data only. Write the amendment before any code reads the test stream of that workload.

**Rule.** On the freeze date, commit this protocol to the public GitHub organization. Record the date and the time of that commit. That record is the freeze timestamp.

**Rule.** After the freeze, do not change a rule in the place where it stands.

**Rule.** After the freeze, write each change as a dated amendment. Say what changed. Say why it changed.

**Rule.** Do not use the test stream until the protocol is frozen.

**Explanation.** A freeze makes the test public before the first run. A reader can then see that the pass rules were not changed to fit the result. A later change is still possible. The change must be an amendment, and the amendment must show its date.

