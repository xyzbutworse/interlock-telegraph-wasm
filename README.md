# INTERLOCK Telegraph Scorer v11

Compact WebAssembly scorer for Telegraph's `ONCHAIN_TX_LOOKUP` intent.

Status: published artifact, not yet registered or accepted by Telegraph.

## Selected artifact

- File: `interlock_static_potion_v11_compact80.wasm`
- Size: `3,039,191` bytes
- SHA-256: `82a5c3bbb3f845de1f38ded3cc0bda75e742124efa631df3552388a1fe9d3d13`
- Keccak-256: `0x04c566b197d8102a850a3fc1ada87752fbc508adcb312b1eb2ae3521e0a988da`
- Telegraph intent: `ONCHAIN_TX_LOOKUP`
- WASM imports: `0`
- Required exports: `memory`, `alloc`, `dealloc`, `rank_answer`

V11 preserves v10 scoring logic and changes one mechanism. The frozen 256-dimensional embedding table is projected to 80 dimensions with deterministic non-centered PCA, followed by per-row INT8 quantization.

The selected module is 63.10 percent smaller than v10.

## Measured verification

- ABI and corpus checks: 23 of 23
- Focused ordering: 15 of 15
- Broad discrimination: 29 of 32
- Worst self-match: 1.0
- Rust scorer tests: 32 of 32
- Repository tests: 185 of 185
- Fresh release rebuild: byte identical
- Wazero interpreter stress: 300 long-input calls in 14.89 seconds
- Linear memory before and after stress: 5,242,880 bytes

The 21-pair comparison against v10 preserved ordering at 20 of 21, increased average margin from 0.879315 to 0.883566, introduced no ties, and preserved the public rank proxy within the 0.01 acceptance gate.

## Verify downloaded bytes

```sh
shasum -a 256 interlock_static_potion_v11_compact80.wasm
```

Expected output:

```text
82a5c3bbb3f845de1f38ded3cc0bda75e742124efa631df3552388a1fe9d3d13  interlock_static_potion_v11_compact80.wasm
```

Use a raw GitHub URL pinned to the release commit SHA. A commit-pinned URL keeps the served bytes immutable.

## Evidence boundary

The metrics above come from public local corpora and local runtimes. Telegraph private fixtures and node delivery remain unknown until evaluation. Publication does not prove registration or acceptance.
