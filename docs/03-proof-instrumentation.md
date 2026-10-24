# Tier 1–4 proof instrumentation

[Documentation index](README.md)

The proving workload across the PSG hypergraph and SMT lowering through MLIR.

HelloFHE is a proving ground for the verification machinery as well as encrypted arithmetic. Its deliverable is an inspectable derivation from the first-party Clef source through the PSG and the MLIR/SMT seam to the accepted realization. The library supplies checked arithmetic laws and relational rules; the compiler derives their use-site obligations from the actual program. Application authors do not reproduce the proof at each call.

Use Fidelity.Cryptography's [Clef proof plan](../../Fidelity.Cryptography/docs/clef-proof-plan.md) and [verification owner](../../Fidelity.Cryptography/docs/verification.md), together with Composer's [proof-composition architecture, lines 1–70](https://forge.spkez.dev/FidelityFramework/Composer/src/commit/ee0e581706549ac1881b0dfd8015cb9221312dea/docs/Proof_Composition_Architecture.md#L1-L70). Those existing owners govern theorem admission, actual participants, mode shifts and transitive premises. This section specifies the HelloFHE workload and the instrumentation it must expose; it does not introduce a second proof service or a new source annotation syntax.

## Start with one visible arithmetic law

The first kernel is modular addition, with a checked producer supplying each operand bound:

```text
q > 1
0 <= a < q, 0 <= b < q
s = a + b                    exact, with capacity through 2q - 2
r = s mod q
------------------------------------------------------------
0 <= r < q, and r is congruent to a + b modulo q
```

The demonstration should show the actual `a`, `b`, `q`, widened `s`, reduction and result as participants in one joint obligation. Prove that the selected Clef representation covers the intermediate; do not let machine wraparound silently replace the specified addition. This extends the library's [existing modular-law scaffold](../../Fidelity.Cryptography/proofs/README.md), using the BFV modulus and the actual operation instance.

Next admit negacyclic polynomial multiplication. For coefficient lifts bounded in absolute value by `B`, a schoolbook product coefficient is bounded by `N * B * B` before any additional cross-term factor. Retain the signed fold at `X^N = -1`, actual accumulator bounds, exact capacity and reduction semantics. BFV's scale/round kernel then needs its specified lifted inputs, rounding convention, widened products and error relation. The modular-addition proof does not automatically justify either of these operations.

## The tiered workload

| Tier | HelloFHE judgments | Instrumentation to inspect |
| --- | --- | --- |
| 1 | Admitted key purposes, context/parameter relationships, coefficient representation, input/output shapes and resource-use rules | Structural derivations tied to the actual Clef values and PSG participants; source locations and admitted interpretations |
| 2 | Array access, loop extents, scratch capacity, intermediate widths, modular-reduction premises and local rounding conditions | Generated verification conditions, selected supported theory, solver/checker dependencies and source-linked outcomes |
| 3 | Modular and polynomial laws, BFV encoding/decryption laws and the selected noise-correctness theorem under its premises | Registered law identity, exact substitutions, theorem instance and every premise consumed at the operation |
| 4 | Scalar/optimized realization correspondence, sampler-distribution relations, compiler preservation and selected leakage/security-game judgments | Relational annotations/rule instances, observation model, coupling or computational assumptions, quantitative bounds and checked derivation |

These classify judgments, not whole routines or mandatory sequential stages. Tier 2 range evidence may supply a Tier 4 premise directly. Tier 4 has the wider relational remit; this workload exercises selected arithmetic-realization, probabilistic and leakage relations. The hardness premise of the selected scheme remains named rather than being replaced by arithmetic solving.

## PSG instrumentation

Extend the owning CCS/Baker obligation and evidence records. Keep the operation, operands, representation, parameter profile, bound producers, region/lifetime facts and governing theorem in the same hypergraph relation. For encryption and evaluation, retain the plaintext encoding relation, ciphertext component count, source polynomial identity and symbolic noise model. A two-component input and three-component result must not acquire a two-component-only decryption theorem by a textual name match.

Expose the following information through the existing source-linked proof inspection surface:

- Source/revision and PSG operation/hyperedge identity, with every participant's role.
- The obligation, declared mode/stage, law or derivation identity and exact parameter substitution.
- Premise producers and their dependency closure, including target arithmetic and admitted randomness premises.
- Discharge status and evidence identity; unresolved, unsupported and refuted obligations remain distinguishable.
- The authorized lowering edge and correspondence to its receiving representation.

These are views of compiler-owned facts. A hand-maintained JSON checklist cannot become their authority. Instrumentation records program identities, public parameters and symbolic facts; it must not dump keys, sampled secrets, random seeds or client plaintext values.

## SMT lowering through MLIR

Alex witnesses the settled PSG and carries the obligation/evidence correspondence into the admitted MLIR form, including the SMT carrier for the supported local conditions. Retain source/PSG identity and scope with each condition. Standard computation dialects carry the program structure; the middle end does not rediscover encryption semantics, operand bounds or ownership.

For the modular-addition slice, inspect the actual lowered arithmetic and the corresponding precondition/negated-postcondition query. Linear integer facts can discharge suitable extent and capacity premises. Bounded bit-vector arithmetic can express selected machine-width multiply/reduction checks. The chosen theory and encoding must match the claim; an unbounded integer proof cannot silently stand for an overflowing machine operation. Full ring correctness, probabilistic relations and cryptographic games do not become quantifier-free arithmetic merely because the local carrier is SMT.

A preservation-certified edge transports its checked evidence. Otherwise re-dispatch the affected obligation against the lowered form at the matching scope. Record whether the result is solver-dependent or independently checked; a bare success flag is not an admitted Rocq lemma. A timeout, unsupported query or missing semantic correspondence blocks the dependent commitment. Do not erase it while retaining the program artifact as accepted.

The inspection record joins the Clef source, PSG participants, lowered operation/condition, theorem or checker dependencies and final artifact identity. Generated Rocq material belongs to the managed proof pipeline. Any retained trace is a diagnostic projection of that chain, not a parallel authority or runtime proof tag inside ciphertext.

## Show that the instrumentation distinguishes failures

| Deliberate change | Required evidence consequence |
| --- | --- |
| Remove an operand's range-producing guard or contract | Its consumer loses the premise; admission cannot reuse the old modular-law instance |
| Narrow the sum or polynomial accumulator below its bound | The representation/overflow obligation fails or remains unresolved; no silent truncation |
| Replace negacyclic subtraction at the fold with addition | The polynomial-law/realization comparison distinguishes the changed ring operation |
| Change a BFV scale or rounding boundary | The instantiated correctness relation and affected lowering evidence must be re-established |
| Substitute a different parameter/encoding or component count | Context/shape relations and dependent theorem instances are invalidated |
| Change sampling or make a secret control a branch/address | The distribution/leakage judgment is re-established under the declared model; round-trip success cannot satisfy it |
| Drop an obligation, swap operand correspondence or reuse stale evidence during lowering | The source/PSG/MLIR/artifact binding check refuses the commitment |
| Retire a coefficient region while a deferred computation still borrows it | Resource/lifetime and lowered-realization evidence no longer establish the operation |

The first acceptance increment executes the modular-addition positive case and its range/width/binding mutations. It then grows to polynomial multiplication, BFV scale/round, sampling and the full greeting. Each increment reports generated obligations, discharged evidence and residuals together. This is how the demonstration shows its work; the expected `42` is one visible consequence.
