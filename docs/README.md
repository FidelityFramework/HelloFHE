# HelloFHE documentation

HelloFHE uses a synchronous encrypted square/add to exercise first-party Clef cryptography and Tier 1-4 proof instrumentation in the PSG and MLIR/SMT lowering.

## Reading order

| Document | Scope |
| --- | --- |
| [00: Overview and foundations](00-overview-and-foundations.md) | Framework ownership and the existing work that HelloFHE exercises. |
| [01: The synchronous demonstrator](01-synchronous-demonstrator.md) | The first application flow, its local call surfaces and resource retirement. |
| [02: First-party Clef cryptography](02-first-party-cryptography.md) | The scalar BFV implementation plan, representation and custody contracts, followed by packed semantics. |
| [03: Tier 1-4 proof instrumentation](03-proof-instrumentation.md) | The proving workload across the PSG hypergraph and SMT lowering through MLIR. |
| [04: Cache-aware compilation](04-cache-aware-compilation.md) | Layout, SIMD kernels, placement and the obligations for optimized Clef realizations. |
| [05: Actor lifecycle and ciphertext storage](05-actor-lifecycle-and-storage.md) | Optional service hosting, bounded admission, terminal outcomes and MBS persistence. |
| [06: Implementation and acceptance](06-implementation-and-acceptance.md) | Ordered increments, discriminating checks and measurement requirements. |
| [07: Sources and provenance](07-sources-and-provenance.md) | Repository revisions, inspected spans, retrieval scope and reference boundaries. |
| [08: OpenFHE study and attribution](08-openfhe-study-and-attribution.md) | Local checkouts, inspected sources and their use in original Clef algorithms and proofs. |

Read 00 through 03 for the first demonstrator and its proof path. Documents 04 and 05 cover optimization and service extensions. Document 06 lists implementation and acceptance gates, 07 records provenance, and 08 maps the OpenFHE sources used in the study.

## Ownership

Update the relevant numbered document when a design changes. Add new topics to this index. Cryptographic operations and laws belong in Fidelity.Cryptography, target premises in Fidelity.Platform, and compiler evidence in the PSG and lowering components.

[Project overview](../README.md) · [Clef sources](../src/Program.clef) · [Project manifest](../HelloFHE.fidproj)
