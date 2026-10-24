# HelloFHE documentation

HelloFHE develops a synchronous encrypted square/add in first-party Clef and uses it to exercise Tier 1–4 proof instrumentation in the PSG and MLIR/SMT lowering. The application source is a draft; native encrypted execution and its proof gates remain to be established.

## Reading order

| Document | Scope |
| --- | --- |
| [00 — Overview and foundations](00-overview-and-foundations.md) | Framework ownership and the existing work that HelloFHE exercises. |
| [01 — The synchronous demonstrator](01-synchronous-demonstrator.md) | The first application flow, its local call surfaces and resource retirement. |
| [02 — First-party Clef cryptography](02-first-party-cryptography.md) | The scalar BFV implementation plan, representation and custody contracts, followed by packed semantics. |
| [03 — Tier 1–4 proof instrumentation](03-proof-instrumentation.md) | The proving workload across the PSG hypergraph and SMT lowering through MLIR. |
| [04 — Cache-aware compilation](04-cache-aware-compilation.md) | Layout, SIMD kernels, placement and the obligations for optimized Clef realizations. |
| [05 — Actor lifecycle and ciphertext storage](05-actor-lifecycle-and-storage.md) | Optional service hosting, bounded admission, terminal outcomes and MBS persistence. |
| [06 — Implementation and acceptance](06-implementation-and-acceptance.md) | Ordered increments, discriminating checks and measurement requirements. |
| [07 — Sources and provenance](07-sources-and-provenance.md) | Repository revisions, inspected spans, retrieval scope and reference boundaries. |
| [08 — OpenFHE study and attribution](08-openfhe-study-and-attribution.md) | Local checkouts, precise reading map and how their lessons inform original Clef algorithms and proofs. |

Start with **00 → 01 → 02 → 03** for the first demonstrator and its proof path. **04** and **05** develop optimization and service extensions. **06** owns the implementation sequence and acceptance gates; **07** owns source provenance. **08** maps the local OpenFHE study sources and their influence on the first-party work.

## Ownership

Each numbered document owns its topic. Update that document when its design changes, and update this index when adding a topic. Keep reusable cryptographic operations and laws with Fidelity.Cryptography, target premises with Fidelity.Platform, and compiler evidence with the existing PSG/lowering owners.

[Project overview](../README.md) · [Clef sources](../src/Program.clef) · [Project manifest](../HelloFHE.fidproj)
