# English embedding model

The English fork uses `onnx-community/granite-embedding-small-english-r2-ONNX` with q8 weights, mean pooling, normalized 384-dimensional vectors, and local inference. First use downloads the model; subsequent runs use the local cache.

## Verification

Run `ENGRAM_E2E=1 pnpm test` to include the real model download and inference test. Explicit opt-in fails on network/model errors instead of silently skipping. The test checks dimensions, finite values, normalization, and English semantic similarity.

## Existing memory databases

Legacy 512-dimensional vectors are not comparable with the new model. Retrieval and consolidation reject mismatched dimensions. Existing memory text is not changed automatically.

Stop Harness before rebuilding vectors. First inspect the dry-run report:

```sh
node scripts/reembed-after-english.mjs /path/to/engram
```

Back up the database directory, then run during a maintenance window:

```sh
node scripts/reembed-after-english.mjs /path/to/engram --apply --maintenance-window
```

The migration only updates missing or legacy-size vectors and guards against concurrent text changes. It does not translate stored memory text.

This change preserves the fork's existing v0.7.15 source features. The older locally translated v0.7.13 bundles are intentionally not copied over newer code.
