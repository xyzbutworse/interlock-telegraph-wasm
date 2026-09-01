# INTERLOCK Telegraph scorer v14

Compact WebAssembly scorer for Telegraph's `ONCHAIN_TX_LOOKUP` intent.

Status: published artifact, not registered or accepted by Telegraph.

## Selected artifact

- File: `interlock_static_potion_v14_correctness.wasm`
- Size: `3,046,966` bytes
- SHA-256: `6c2f18ca4f93cbe98b2c260378ccea9c401b53df4559045ff600a3df11844114`
- Keccak-256: `0x81a53a20434a06efc61250568069fd6940eba6acab0f32d60f8803ae86696765`
- Telegraph intent: `ONCHAIN_TX_LOOKUP`
- WASM imports: `0`
- Required exports: `memory`, `alloc`, `dealloc`, `rank_answer`

V14 keeps the compact 80-dimensional embedding table from v11/v12 and rewrites the
natural-language scoring to optimize one objective: **score correct answers high and
wrong or evasive answers low.** Ordering over the champion follows from correct scoring
rather than being tuned for directly.

## Scoring changes

Two root-cause classes of miscalibration in earlier versions are fixed.

**Number values, not number spelling.** Numbers are now read as canonical values
rather than matched as literal tokens. Digit grouping (`18,450,221` = `18450221`),
decimal formatting (`2.5` = `2.50`, and distinct from `5.2` and `25`), and hex case
(`0xAbC` = `0xabc`) no longer change the comparison. Earlier versions floored a correct
answer to `~0.002` whenever it wrote a number differently from the ground truth.

**Polarity, not bag-of-word negation.** Negation is handled on valence and existence
axes with clause-scoped negation windows and clause-level determiner negation, so
"did not fail" reads as success, "contains no transfer events" and "No new contract was
deployed" read as negative existence, and a bare shared negator ("the data does not
say") no longer covers a negated truth. A correct answer that omits a corroborating
number is credited on its polarity; an answer that states a *foreign* number is not.

## Verification (public local corpora and runtimes)

Correctness calibration over diverse phrasings and formats:

| Class | Mean score | Requirement | Result |
|---|---:|---|---|
| Correct answers | 0.949 | all ≥ 0.70 | pass |
| Wrong answers | 0.002 | all ≤ 0.30 | pass |
| Evasive non-answers | 0.002 | all ≤ 0.30 | pass |

Ordering and discrimination against champion 642:

- Public ordering gate: 15/15, margin 0.9156 (champion 0.8566)
- Broad adversarial corpus (305 pairs): candidate 291/305, champion 290/305
- Candidate mean margin 0.856 versus champion 0.419

Runtime and safety:

- Rust scorer tests: 21/21
- Wazero interpreter per-call: ~2.97 ms (compiler ~0.04 ms) — no timeout risk
- WASM imports: 0
- Scores bounded to `[0,1]`; deterministic repeated scores; worst self-match 1.0
- Fresh release rebuild: byte identical

## Verify downloaded bytes

```sh
shasum -a 256 interlock_static_potion_v14_correctness.wasm
```

Expected output:

```text
6c2f18ca4f93cbe98b2c260378ccea9c401b53df4559045ff600a3df11844114  interlock_static_potion_v14_correctness.wasm
```

Use a raw GitHub URL pinned to the release commit SHA.

## Known limitation

Compound written numerals ("twenty-one thousand") are not parsed to their digit value
and may score low; digit and single-word numeral forms ("21,000", "seven") are handled.

## Evidence boundary

All measurements come from public local corpora and local runtimes. Telegraph private
fixtures remain unknown. Publication does not prove registration or acceptance.
