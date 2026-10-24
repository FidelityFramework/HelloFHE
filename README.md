# HelloFHE

A small Clef program toward a fully homomorphic encryption greeting: give an evaluator an encrypted integer, calculate `x * x + x`, and decrypt the answer on the client. For input `6`, the answer is `42`.

The greeting is: **You gave me something I could not read. I computed with it. Here is an answer only you can open.**

The cryptographic implementation belongs in first-party Clef within Fidelity.Cryptography. OpenFHE and published algorithms provide reference material. HelloFHE also exercises Tier 1–4 proof instrumentation in the PSG hypergraph and SMT lowering through MLIR; printing the expected answer alone is not its acceptance gate.

## Current state

The source drafts a synchronous encrypt/evaluate/decrypt flow with explicit cleanup. The default entry point still runs the clear reference. Native compilation, encrypted execution and the associated proof gates have not been run for this application.

| File | Responsibility |
| --- | --- |
| [Arithmetic.clef](src/Arithmetic.clef) | Input admission and one shared square/add sequence |
| [Protocol.clef](src/Protocol.clef) | Local client/evaluator call surfaces and later message shapes |
| [Experiment.clef](src/Experiment.clef) | Synchronous encrypted flow and resource retirement |
| [Program.clef](src/Program.clef) | Clear-reference entry point |
| [HelloFHE.fidproj](HelloFHE.fidproj) | Console project and ordered source files |

The admitted clear input range is `0..100`, with results in `0..10100`. The first-party library must supply the cryptographic operations before the encrypted path can run. The first milestone uses one synchronous executable; packing, cache-aware SIMD, process isolation, actors and storage are later extensions.

## Documentation

[Start with the documentation index](docs/README.md). The numbered documents separate foundations, the demonstrator, cryptography, proofs, optimization, service lifecycle, acceptance and sources.

## Working with Composer

The manifest selects the sibling Linux x86_64 platform package at `../Fidelity.Platform/Profiles/Linux_x86_64_Default/Fidelity.Platform.fidproj`. Another deployment needs its corresponding admitted platform selection. Source order is arithmetic, protocol, experiment, entry point.

Use the established Bozzetto/Composer workflow for native validation: open the `.fidproj`, reserve before editing source, build through the provider and run the accepted artifact through `composer_run_current`. Local controller authority and required build/test leases still apply. No build or encrypted-run command is advertised as already validated by this scaffold.

Work in this primary checkout and keep scratch fixtures, logs and generated artifacts under ignored `build/`. The [sources and provenance](docs/07-sources-and-provenance.md) identify the implementation references and evidence boundaries.
