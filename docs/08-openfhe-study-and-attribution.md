# Studying OpenFHE and crediting its influence

[Documentation index](README.md)

These checkouts are technical study material for original Clef cryptography. We learn how the algorithms work, cite the sources that inform our decisions, and write the Fidelity implementation from its mathematical contracts and proof requirements. We are not porting OpenFHE, translating its source into Clef, or presenting its implementation as our own.

## The local reference set

Located under `/home/hhh/repos` on 2026-10-23. All three checkouts were on `main` with clean working trees at inspection. The revisions below identify the inspected local sources.

| Checkout | Inspected revision | Role in our study |
| --- | --- | --- |
| [openfhe-development](../../openfhe-development/README.md) | `b59e1f291965f14fbee5172cd83a20f5b856204f` | BFV operation structure, parameter/context relationships, ciphertext components, scaling and rounding |
| [openfhe-hexl](../../openfhe-hexl/README.md) | `11f2f6afee25b4b61fc6c785a975765572c7438b` | Modular vector arithmetic, transform preconditions and the separation of arithmetic semantics from an accelerated realization |
| [openfhe-python](../../openfhe-python/README.md) | `4f13e2c3a7e35f73f4816904dabd3a3db47b6e51` | Understandable experiment sequences, observable results and test shapes |

The HEXL checkout's [README, lines 1-10](https://github.com/openfheorg/openfhe-hexl/blob/11f2f6afee25b4b61fc6c785a975765572c7438b/README.md#L1-L10) describes staging for OpenFHE 1.5.1 and Intel HEXL 1.2.6. A matched executable build of the three checkouts has not been established. No build, install, binding or reference run was performed for this note.

## Source observations

### BFV: operation stages

The development [integer example, lines 44-121](https://github.com/openfheorg/openfhe-development/blob/b59e1f291965f14fbee5172cd83a20f5b856204f/src/pke/examples/simple-integers.cpp#L44-L121) separates context selection, key generation, encoding/encryption, evaluation and decryption. It uses packed vectors, depth two, relinearization and rotation keys. Use those phases to identify the dependencies of each operation.

HelloFHE requires a scalar `x*x+x` with depth one. Determine the state required by each phase. Derive our key material, encoding and lifetimes from that requirement. Keep the original source expression in `Arithmetic.clef`; the library owns the Clef cryptographic operations. Do not reproduce the example's API sequence, containers or extra operations as the architecture of our program.

### BFV: component growth and scale/round

The inspected multiplication [entry, lines 200-229](https://github.com/openfheorg/openfhe-development/blob/b59e1f291965f14fbee5172cd83a20f5b856204f/src/pke/lib/scheme/bfvrns/bfvrns-leveledshe.cpp#L200-L229) checks parameter compatibility, obtains component counts and sizes the result. The [product and scale/round portion, lines 326-398](https://github.com/openfheorg/openfhe-development/blob/b59e1f291965f14fbee5172cd83a20f5b856204f/src/pke/lib/scheme/bfvrns/bfvrns-leveledshe.cpp#L326-L398) distinguishes component products, representation changes, rounding and basis conversion. The inspected spans cover part of the routine. Other multiplication variants were not reviewed.

Divide the proof into component shape, ring-product semantics, exact intermediate capacity, scale/round semantics and representation correspondence. Compose these results over the same participants. A correct polynomial tensor product alone does not establish BFV ciphertext multiplication. Neither does a successful encrypt/decrypt round trip.

For example, multiplying two two-component ciphertexts produces three components before relinearization. In Fidelity, that mathematical shape becomes a relation between the actual input and output arrays in the PSG. The matching extent/capacity conditions follow into MLIR/SMT. The BFV law supplies the semantic relation under its actual parameter and noise premises. The resulting proof application concerns the Clef implementation.

### BFV: relinearization and decryption

The development [multiplication/relinearization overloads, lines 712-729](https://github.com/openfheorg/openfhe-development/blob/b59e1f291965f14fbee5172cd83a20f5b856204f/src/pke/lib/scheme/bfvrns/bfvrns-leveledshe.cpp#L712-L729) make their separation explicit. The [decryption excerpt, lines 225-277](https://github.com/openfheorg/openfhe-development/blob/b59e1f291965f14fbee5172cd83a20f5b856204f/src/pke/lib/scheme/bfvrns/bfvrns-pke.cpp#L225-L277) also distinguishes decryption-core output, coefficient form and rounding paths. We inspected this wrapper, not the body of `DecryptCore`.

Use those distinctions to formulate the first-party contracts in [02](02-first-party-cryptography.md). The proposed initial three-component path requires our own component-count, decryption and rounding relation. It is not admitted by the existence of an upstream overload. A later Clef relinearization implementation must preserve that same plaintext relation while changing the representation and resource footprint.

The BFV implementation [identifies a further algorithm reference at lines 32-34](https://github.com/openfheorg/openfhe-development/blob/b59e1f291965f14fbee5172cd83a20f5b856204f/src/pke/lib/scheme/bfvrns/bfvrns-leveledshe.cpp#L32-L34). Follow such references to establish the exact mathematical variant before choosing an optimization. That paper was not reviewed in this increment, and the source's reference is not a substitute for reading it.

### HEXL: modular arithmetic and transforms

The HEXL [modular-addition code, lines 91-125](https://github.com/openfheorg/openfhe-hexl/blob/11f2f6afee25b4b61fc6c785a975765572c7438b/src/core/lib/math/hal/intnat-hexl/mubintvecnathexl.cpp#L91-L125) and [multiplication code, lines 171-215](https://github.com/openfheorg/openfhe-hexl/blob/11f2f6afee25b4b61fc6c785a975765572c7438b/src/core/lib/math/hal/intnat-hexl/mubintvecnathexl.cpp#L171-L215) expose modulus/length compatibility and accelerated versus scalar paths. The [forward transform, lines 590-629](https://github.com/openfheorg/openfhe-hexl/blob/11f2f6afee25b4b61fc6c785a975765572c7438b/src/core/include/math/hal/intnat-hexl/transformnathexl-impl.h#L590-L629) and [inverse excerpt, lines 688-721](https://github.com/openfheorg/openfhe-hexl/blob/11f2f6afee25b4b61fc6c785a975765572c7438b/src/core/include/math/hal/intnat-hexl/transformnathexl-impl.h#L688-L721) expose transform-size checks, roots/moduli, cached public transform state and completion of the transform call.

For each Clef kernel, determine the input residue range, widened product capacity, transform root/order and permutation, reusable public tables and permitted aliases. Derive those contracts and author our scalar and optimized algorithms around them. Do not transplant pointer casts, cache containers, preprocessor choices or acceleration calls. Platform supplies target features; Baker settles admitted representation and scheduling; the backend realizes our checked Clef operations.

An NTT inverse/forward round trip is one check. The stronger relation connects transformed multiplication to the specified negacyclic product, with the root/order, normalization and representation fixed. Cache performance and SIMD gains then concern that same proved computation. See [04](04-cache-aware-compilation.md) for the optimization plan.

### Python: examples and assertions

The Python [integer example, lines 7-75](https://github.com/openfheorg/openfhe-python/blob/4f13e2c3a7e35f73f4816904dabd3a3db47b6e51/examples/pke/simple-integers.py#L7-L75) shows what the caller supplies and observes. Its cryptographic implementation is still OpenFHE.

The [BGV test excerpt, lines 75-104](https://github.com/openfheorg/openfhe-python/blob/4f13e2c3a7e35f73f4816904dabd3a3db47b6e51/tests/test_bgv.py#L75-L104) compares decrypted arithmetic and rotation results with independently calculated clear expectations. It tests BGV behavior; BFV requires its own correctness argument. Learn the testing principle and construct HelloFHE's own cases from its own input bounds, encodings and contracts. Do not copy the test body or mistake its deterministic plaintext-data generator for a cryptographic randomness source.

Our initial cases remain `0`, `6`, `100`, rejected `-1` and `101`, plus the failure controls in [03](03-proof-instrumentation.md) and [06](06-implementation-and-acceptance.md). The later packed cases follow our declared lane map. Foreign ciphertext byte equality is not the general correctness criterion: randomness, encoding and internal representation must be accounted for before any comparison has meaning.

## Clef implementation and proofs

1. State the mathematical operation. Record the scheme/variant, input and output meaning, distributions, representation and required relation. Cite the mathematical specification and the exact OpenFHE source that clarified an implementation issue. An upstream routine name is a reading locator, not the definition of our function.
2. Derive the Fidelity obligations. Attach the actual values, profile, shapes, bounds, regions and theorem premises to their PSG relation. Decide which local conditions are expressible in the supported SMT fragment and which domain/relational laws are required.
3. Author the Clef algorithm. Choose the scalar arithmetic and bounded array/loop structure that expresses those contracts within the current language. The source is written for Clef's inferred representations, deferred bindings and deterministic resource management. It is not a line-by-line or structural translation of a foreign routine.
4. Show the work through lowering. Discharge the registered law's actual premises and retain participant identity through MLIR/SMT. Transport certified evidence or re-dispatch at the seam. Tier 4 relates our scalar/optimized realizations, distributions and observations under the chosen rules. No proof claim is made over the C++ or Python reference implementation.
5. Test the claim and its failures. Calculate expectations from the specification, then exercise incorrect widths, folds, scales, context/component counts and premature retirement. A later independently run reference comparison can supply additional diagnostic evidence when its parameters and encoding match. It neither becomes a runtime dependency nor replaces the Clef proof.
6. Credit the influence precisely. At the relevant algorithm/design discussion, name the source, revision and inspected lines, what we learned, and the decision it informed. Credit an established mathematical technique as established work; distinguish our implementation and verification contribution. Identify the established techniques used and the sources consulted.

Record the sources consulted and credit the ideas used. Write the Clef implementation and proofs from the mathematical contracts. Renaming identifiers or translating source would still be copying.

## Implementation tasks

| Study result | First-party work it informs | Evidence to produce |
| --- | --- | --- |
| Separate parameter admission from operation execution | Clef parameter/context contracts in Fidelity.Cryptography | PSG relationships and rejection of incompatible profiles |
| Separate component shape from scale/round | Scalar ring product and BFV multiplication | Shape/width conditions in MLIR/SMT plus checked domain-law instances |
| Separate multiplication from relinearization | First three-component path, then optional Clef relinearization | Decryption/component correspondence and later realization relation |
| Separate scalar arithmetic from acceleration | Original Clef modular kernels, then NTT/SIMD | Scalar laws, transform correctness and optimized-realization evidence |
| Separate visible experiment from cryptographic internals | HelloFHE's existing synchronous application | Expected clear answers, stage failures and resource retirement |

See [02](02-first-party-cryptography.md) for cryptographic design, [03](03-proof-instrumentation.md) for instrumentation, and [06](06-implementation-and-acceptance.md) for acceptance gates. This note records OpenFHE sources and attribution. No OpenFHE code, test body, build recipe or runtime dependency is imported by this documentation work.

## Inspection scope

The pinned excerpts above and the three repository READMEs were read directly from the user's existing checkouts. They are outside the published Fidelity retrieval corpus at snapshot `26be94971a5a2ab5dd24156c88ca6e331d6eb81ee1e81de0fb98118abab02416`, so the outside-index exception applies. The checkout inventory establishes presence and revision; the source excerpts establish the specific observations. No whole-repository algorithm audit, upstream proof reuse, native execution or performance result is claimed.
