# INTERLOCK Telegraph Scorer v12

Compact WebAssembly scorer for Telegraph's `ONCHAIN_TX_LOOKUP` intent.

Status: published artifact, not yet registered or accepted by Telegraph.

## Selected artifact

- File: `interlock_static_potion_v12_graded_negation.wasm`
- Size: `3,039,223` bytes
- SHA-256: `6a5835643909686d7756312e31019482427468801779217c39557595cb7a8833`
- Keccak-256: `0x082778ef72f5c1354df6549ba47afa69a376b19f5df2856ff0367efe82d19155`
- Telegraph intent: `ONCHAIN_TX_LOOKUP`
- WASM imports: `0`
- Required exports: `memory`, `alloc`, `dealloc`, `rank_answer`

V12 keeps v11's compact80 embedding table (256-dimensional frozen embedding projected to 80 dimensions with deterministic non-centered PCA and per-row INT8 quantization) and changes one mechanism in the natural-language scoring path.

## What changed

Previous versions applied a **hard `0.0` floor** whenever the ground truth and the answer differed in the mere presence of a negator word (`has_negation(gt) != has_negation(answer)`). Presence of a negator is not proof of opposite meaning: a correct paraphrase such as "the transaction did not fail" (meaning it succeeded) or "no longer pending" (meaning finalized) tripped that floor and was slammed to `0.0`, which could rank a correct-but-negated answer **below** an evasive wrong answer.

V12 replaces that hard floor with a **graded multiplicative penalty** (`NEGATION_MISMATCH_FACTOR = 0.35`). Genuine opposite-meaning pairs are still hard-floored by the axis-aware `polarity_conflict` check, which flips a word's polarity when a negator precedes it, so true antonyms (success vs reverted, exists vs missing) remain separated. The change removes the cliff that penalized correct negated paraphrases while keeping wrong answers pushed down.

## Measured verification

Scope: public local corpora and local `wazero`/WebAssembly runtimes. Numbers below were reproduced for this build.

- Focused ordering gate (candidate vs champion 642): 15 of 15, margin 0.9602 vs champion 0.8566
- Rust scorer host tests: 14 of 14, including a new regression (`correct_negated_paraphrase_beats_evasive_wrong_answer`)
- Held-out negator-trap probe (cases the public gate does not contain): candidate 3 of 5, matching the champion at 3 of 5; the two shared failures are pairs the champion also loses
- Worst self-match: 1.0
- WASM imports: 0

The graded penalty preserved every case the previous hard floor separated on the public ordering gate, and additionally recovers correct-but-negated answers that the hard floor inverted.

## Verify downloaded bytes

```sh
shasum -a 256 interlock_static_potion_v12_graded_negation.wasm
```

Expected output:

```text
6a5835643909686d7756312e31019482427468801779217c39557595cb7a8833  interlock_static_potion_v12_graded_negation.wasm
```

Use a raw GitHub URL pinned to the release commit SHA. A commit-pinned URL keeps the served bytes immutable.

## Evidence boundary

The metrics above come from public local corpora and local runtimes. The public ordering gate is an explicit approximation of Telegraph's private gate; passing it does not guarantee the private fixtures. Telegraph private fixtures and node delivery remain unknown until evaluation. Publication does not prove registration or acceptance.
