# INTERLOCK Telegraph scorer v12

Compact WebAssembly scorer for Telegraph's `ONCHAIN_TX_LOOKUP` intent.

Status: published artifact, not registered or accepted by Telegraph.

## Selected artifact

- File: `interlock_static_potion_v12_soft_negation.wasm`
- Size: `3,039,223` bytes
- SHA-256: `cf9e6c758f9b78b5fec6775e840b2e3bde27d76560eed85b3b3028a6449483d6`
- Keccak-256: `0x2cf753f1f436c0ffd8598f4655c11e7581997ff8f18ea5b5d887b96020ad40b8`
- Telegraph intent: `ONCHAIN_TX_LOOKUP`
- WASM imports: `0`
- Required exports: `memory`, `alloc`, `dealloc`, `rank_answer`

V12 retains v11's compact 80-dimensional embedding table and changes one scoring mechanism.

## Single scoring change

V11 multiplied scores by `0.0` when ground truth and answer differed only in surface negator presence. This hard floor inverted correct paraphrases such as "the transaction did not fail" when compared with an evasive wrong answer.

V12 uses a bounded `0.35` multiplier for this bare mismatch. The axis-aware polarity detector still floors direct contradictions such as success versus failure.

ABI paths, embeddings, tokenization, question logic, roles, anchors, structured scoring, input bounds, and final score mapping remain unchanged.

## Measured comparison against v11

| Metric | V11 | V12 |
|---|---:|---:|
| Adversarial ordering | 20/21 | 21/21 |
| Average margin | 0.883566 | 0.894983 |
| Minimum margin | 0.000000 | 0.008805 |
| Score standard deviation | 0.489522 | 0.484865 |
| Ties | 1 | 0 |
| Public rank proxy | 0.917138 | 0.917138 |
| Browser P95 | 0.021187 ms | 0.021500 ms |

The same-source hard-floor control reproduced all 21 frozen v11 adversarial scores. Only two negation rows changed in v12. The other 19 adversarial rows and all 125 public proxy rows retained their scores.

## Verification

- Focused ordering against champion 642: 15/15, margin 0.9602
- Broad discrimination: 29/32 versus champion 24/32
- ABI and corpus suite: 23/23
- Rust scorer tests: 33/33
- Repository tests: 185/185
- Deterministic repeated scores: pass
- Scores bounded to `[0,1]`: pass
- Worst self-match: 1.0
- Wazero interpreter stress: pass with fixed memory
- Fresh release rebuild: byte identical

## Verify downloaded bytes

```sh
shasum -a 256 interlock_static_potion_v12_soft_negation.wasm
```

Expected output:

```text
cf9e6c758f9b78b5fec6775e840b2e3bde27d76560eed85b3b3028a6449483d6  interlock_static_potion_v12_soft_negation.wasm
```

Use a raw GitHub URL pinned to the release commit SHA.

## Evidence boundary

All measurements come from public local corpora and local runtimes. Telegraph private fixtures remain unknown. Publication does not prove registration or acceptance.
