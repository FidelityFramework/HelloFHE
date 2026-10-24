# Implementation and acceptance

[Documentation index](README.md)

## Implementation sequence

`Arithmetic.clef`, `Protocol.clef` and `Experiment.clef` contain the reference, call interfaces and synchronous control flow. Keep the application expression in `Arithmetic.clef` and extend the existing framework passes. Complete the synchronous run before packing, optimization and actor hosting.

| Increment | Owning work | Deliverable and acceptance gate |
| --- | --- | --- |
| 1. Admit the reference | `Arithmetic.clef`, `Program.clef`, current Composer project | Native `0`, `6`, `100` results and `-1`, `101` rejection, bound to source/compiler/platform identities |
| 2. Establish the first-party kernel | Extend Fidelity.Cryptography arithmetic and law owners, with Platform target premises | Implement scalar modular arithmetic, negacyclic multiplication and BFV scale/round in Clef; show operand-bound, residue, capacity and lowering obligations in PSG and MLIR/SMT |
| 3. Run the synchronous greeting | Extend the same Clef library with sampling, key generation, encoding, encryption and decryption; construct the calls consumed by `Experiment.clef` | Connect the admitted BFV operations, run `0`, `6`, `100`, and inspect Tier 1-4 evidence and stage-failure/cleanup controls; no actor required |
| 4. Admit packed SIMD execution | Extend the same Cryptography provider for packed calls; existing CCS/Baker, Alex and CPU backend owners carry the derived plan | Pack/encrypt/evaluate/decrypt `[0,6,100]`; compare partial/full batches and accepted scalar/vector realizations; report packing and coefficient SIMD separately |
| 5. Realize cache-aware kernels | Extend the same kernel/scheduling owners | Implement and prove the optimized Clef modular kernel, then NTT/relinearization recipes; compare scalar and optimized outputs, tail shapes, lazy-reduction limits and seam evidence; measure legal tiles under a fixed crypto profile |
| 6. Establish the process boundary | Extend `Protocol.clef`; `Worker.clef` and client transport use BAREWire | Distinct client/worker processes; authenticated bounded framing; worker has no secret/decrypt binding; captured payload remains ciphertext; all costs measured end to end |
| 7. Host the evaluator actor | `Service.clef` binds existing Olivier/Prospero/Ariel contracts | Bounded workers/bytes, completion ownership and cache-domain policy; queue overflow, cancellation, duplicate/late reply, crash/restart and drain controls pass |
| 8. Extend the same service | Existing MBS and GPU owners, with HelloFHE acceptance fixtures | Ciphertext recovery retains context/circuit binding; GPU host dispatch/completion matches the CPU reference and repays transfer/launch costs for a measured batch regime |

Do not replace a missing native operation with identity encryption, a plaintext cast, a handwritten second arithmetic implementation or an invented actor runtime. Record each missing Clef implementation against its contract and unmet acceptance condition.

## Acceptance and measurement

Run these checks as each increment is implemented.

| Boundary | Positive shapes | Discriminating control |
| --- | --- | --- |
| Clear arithmetic | Every integer `0..100` and boundary values | `-1`, `101` rejected before arithmetic |
| Packed semantics | Active lengths `1`, `3`, `S-1`, `S`; split `S+1` into two jobs; padding stays zero | Swap slot order, misstate active length or use an undersized plaintext modulus: contract or comparison fails |
| Parameter/custody | Accepted profile, matching key/context; client-only secret state | Incompatible context, unsupported component count or absent evaluation material for a realization that requires it, insufficient arithmetic/noise profile, secret capture/log or worker decrypt access fails |
| Modular kernels | Scalar/vector agreement on admitted residue limits and vector-tail shapes | Mutate reduction bound, twiddle order or one lane/tail; the kernel equivalence gate distinguishes it |
| Layout and seam | Wire round-trip and compute-map coverage; aligned extents and no forbidden aliases | Corrupt stride/form identity, use stale schedule evidence or reorder a dependent butterfly stage: admission/equivalence fails |
| Admission | Job/byte credits respected at peak live footprint | Oversized expanded carrier, context-install flood or excess concurrent jobs rejected within bounded memory |
| Lifecycle | One terminal outcome per admitted attempt; no arena reuse before completion | Duplicate completion, late callback, wrong epoch, timeout during native use and failed transport do not double-release or publish a second terminal outcome |
| Client residue | Sensitive temporaries retired and cleared under the selected lowered contract | Retained copy or optimized-away clearing fails the residue check |
| Device/persistence | Checked device output; matching ciphertext recovery identities | Read before device completion or restore under a changed context/circuit is rejected |

For performance, record two separate comparisons: packing gain at fixed cryptographic parameters, and cache/SIMD gain at fixed packing and parameters. Start with one packed ciphertext; sweep enough jobs to exceed the measured cache working set and expose contention. Keep key setup and warmup separate from steady state, and also report cold-start and end-to-end latency.

Record client encode/encrypt and decrypt/decode time; serialization, transport and queue time; evaluation substeps; output publication; peak live bytes, copied bytes and active key/table footprint; CPU/topology, ISA dispatch, Clef library revision and build/options, worker/thread budget, source and artifact identities. Report distributions such as median and p95 plus throughput per active lane. Use available cache-miss, bandwidth, instruction and coherence counters with their collection conditions. A missing counter is unknown. HITM samples are not uniquely false sharing.

Accept correctness and required observer-model obligations before performance promotion. Keep the unchanged reference and optimized artifact bindings together. If an optimization loses after marshaling, synchronization or frequency costs, retain that result and select the better admitted schedule. Performance prediction and measured speedup remain separate claims.
