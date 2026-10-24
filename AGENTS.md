# HelloFHE working instructions

## Documentation

- Treat the README and docs as an engineering work surface.
- Specify algorithms, data structures, interfaces, invariants, ownership, implementation steps and tests.
- Keep execution history and status reporting in build receipts. Do not add status disclaimers, validation stamps, unfinished-work warnings or editorial commentary to project documentation.
- Write implementation work as direct instructions. State technical preconditions and failure behavior precisely.
- Use plain technical prose. No em dashes, throat-clearing, promotional language or rhetorical contrasts.
- Keep the numbered `00-`, `01-`, `02-` document structure and the `docs/README.md` index. Edit the relevant topic document.

## Cryptography

- Implement cryptographic algorithms in first-party Clef within Fidelity.Cryptography.
- Study and cite OpenFHE as reference material. Do not copy, port or bind its implementation.
- Develop Tier 1-4 proof instrumentation in the PSG and MLIR/SMT lowering as engineering work. Keep runtime observation separate from the proof workflow.
