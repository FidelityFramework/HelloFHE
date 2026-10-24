# HelloFHE

HelloFHE is a Clef project for evaluating `x * x + x` on encrypted integers. The client encrypts the input and decrypts the result. Input `6` produces `42`.

The cryptographic implementation belongs in first-party Clef within Fidelity.Cryptography. OpenFHE and published algorithms provide reference material. HelloFHE also exercises Tier 1-4 proof instrumentation in the PSG hypergraph and SMT lowering through MLIR. Acceptance requires evidence for the computation and its lowering.

## Source

The source defines a synchronous encrypt/evaluate/decrypt flow with explicit cleanup. `Program.clef` runs the clear reference.

| File | Responsibility |
| --- | --- |
| [Arithmetic.clef](src/Arithmetic.clef) | Input admission and one shared square/add sequence |
| [Protocol.clef](src/Protocol.clef) | Local client/evaluator call surfaces and later message shapes |
| [Experiment.clef](src/Experiment.clef) | Synchronous encrypted flow and resource retirement |
| [Program.clef](src/Program.clef) | Clear-reference entry point |
| [HelloFHE.fidproj](HelloFHE.fidproj) | Console project and ordered source files |

The admitted clear input range is `0..100`, with results in `0..10100`. Connect the first-party cryptographic operations through the client and evaluator interfaces. The first milestone uses one synchronous executable; packing, cache-aware SIMD, process isolation, actors and storage are later extensions.

## Documentation

[Start with the documentation index](docs/README.md). The numbered documents cover foundations, the demonstrator, cryptography, proofs, optimization, service lifecycle, acceptance and sources.

## Working with Composer

The manifest selects the sibling Linux x86_64 platform package at `../Fidelity.Platform/Profiles/Linux_x86_64_Default/Fidelity.Platform.fidproj`. Another deployment needs its corresponding admitted platform selection. Source order is arithmetic, protocol, experiment, entry point.

Use the established Bozzetto/Composer workflow for native validation: open the `.fidproj`, reserve before editing source, build through the provider and run the accepted artifact through `composer_run_current`. Local controller authority and required build/test leases still apply.

Work in this primary checkout and keep scratch fixtures, logs and generated artifacts under ignored `build/`. The [sources and provenance](docs/07-sources-and-provenance.md) identify the implementation references and evidence boundaries.
