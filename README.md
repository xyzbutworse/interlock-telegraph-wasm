# INTERLOCK Telegraph Scorer v10

Verified WebAssembly scorer for Telegraph's `ONCHAIN_TX_LOOKUP` intent.

## Artifact

- File: `interlock_static_potion_v10_v5_bounded.wasm`
- Size: `8,236,087` bytes
- SHA-256: `04361a7837673ca04ae500c639139d936ad3eab266a2afe7cde1a72c546c41e8`
- Keccak-256: `0x4e0f6988422f964581c2e9f46769308b2b5c03e4dfa90cbef9e33331e7a67bdb`
- Telegraph intent: `ONCHAIN_TX_LOOKUP`

V10 restores the node-tested v5 scoring architecture and changes only the input work budget. Public v5 scores remain unchanged across the 21-pair adversarial corpus and the 125-row public rank proxy.

Use the raw GitHub URL pinned to a commit SHA. A commit-pinned URL keeps the served bytes immutable.

## Verification

```sh
shasum -a 256 interlock_static_potion_v10_v5_bounded.wasm
```

Expected output:

```text
04361a7837673ca04ae500c639139d936ad3eab266a2afe7cde1a72c546c41e8  interlock_static_potion_v10_v5_bounded.wasm
```

Local verification:

- ABI and corpus: 23 of 23
- Ordering mirror: 15 of 15
- Discrimination: 29 of 32
- Worst self-match: 1.0
- 300 calls on 128 KiB inputs under wazero interpreter: 11.33 seconds total
- Linear memory before and after the stress run: 10,420,224 bytes
- Isolated rebuild: byte-identical
