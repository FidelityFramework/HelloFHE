# First-party Clef cryptography

[Documentation index](README.md)

## First-party scheme and custody boundary

Use a first-party BFV implementation in Clef, initially with scalar constant-polynomial encoding. The application circuit remains exact integer square/add, at multiplicative depth one. Its admitted inputs are `0..100`, outputs `0..10100`. Packing and approximate CKKS arithmetic are separate later realizations.

Select the ciphertext modulus, ring dimension and secret/error distributions together from a named specification and security profile. Plaintext modulus `t` must cover the output encoding; intermediate representability and noise correctness need their own bounds. For the arithmetic example `N=8192`, `t=65537`, select the ciphertext modulus and distributions to satisfy the security and noise bounds. Use the scheme's specified sampler. Keep test-sized arithmetic fixtures separate from the execution profile.

The [Security Guidelines for Implementing Homomorphic Encryption](https://eprint.iacr.org/2024/463) distinguish parameters for security, correctness and performance. Record all three for the selected variant, including the exact source/version and supported distribution assumptions. These external results inform the premises. Our proof development must establish the Clef implementation's relation to that specified scheme under those premises.

The existing FHE research's [bounds inventory](../../arxiv-papers/research/FHE-modeling/03-bounds-as-witnesses.md) covers the wider derivation program. Name the chosen scheme's actual hardness and evaluation-key assumptions in the profile; do not inherit a generic Module-LWE label without checking the scheme.

Fidelity.Cryptography owns the Clef scheme implementation, arithmetic laws, sampler contracts, encodings and evidence. Implement the cryptography in those library modules. A source library dependency is selected when that implementation and its contracts are available. The application call records consume the admitted operations and retain the same arithmetic expression.

| First-party operation | Preconditions | Result and ownership |
| --- | --- | --- |
| Create client state | Validated public parameter profile and approved randomness source | Client-owned secret key, public encryption material and any evaluation material required by the selected realization |
| Encode and encrypt | Scalar input admitted under the constant-polynomial encoding; later packed realization also admits every lane, length at most `S` and fixed padding | Ciphertext; plaintext staging retired on the client after native completion |
| Load worker session | Matching profile, key-set identity and bounded evaluation material | Read-only public context and evaluation material; no secret key or decrypt operation |
| Evaluate selected circuit | Compatible ciphertext, source/circuit identity and valid resource reservation | Independently owned output ciphertext or explicit failure |
| Serialize/deserialize | Versioned codec, bounded total and expanded lengths, matching context | Owned carrier with checked extents; malformed input rejected before evaluation |
| Decrypt and decode | Client holds matching key; accepted reply binding | Client-owned clear result; no worker decryption or remote noise-budget oracle |
| Destroy/release | No outstanding native use, alias or completion callback | Exactly one release through the matching allocator; secret-state clearing follows its stronger contract |

The table specifies the operation contracts. Keep the evaluator's operation set smaller than the client's. Clef public caches, working regions and sampler state must respect session and concurrency isolation. Warm permitted public tables during session setup, before timed serving.

For the first evaluation, preserve the two-component encrypted input, compute a three-component BFV square into separate storage, add the original with a zero third component, and return the three-component result. The first-party decryptor interprets that declared component count. A later relinearized realization must establish the same plaintext relation. Square-in-place must not destroy the input still needed by the addition. Derive these operations from the source branch so the application formula has one definition.

## Build the cryptographic implementation in Clef

[08: OpenFHE study and attribution](08-openfhe-study-and-attribution.md) identifies the local reference revisions, BFV excerpts and the specific lessons used in this first-party plan.

Implement arithmetic, sampling, encoding, encryption, evaluation and decryption in Fidelity.Cryptography. Study OpenFHE and published algorithms as references. The implementation and proof targets are the Clef sources; C/C++ cryptographic libraries are excluded from this path.

Start with scalar coefficient arrays and public bounded loops. Use an admitted BFV specification and one fixed-depth circuit. Implement coefficient arithmetic, negacyclic polynomial multiplication, the specified scale/round operation, randomness and sampling, key generation, constant-polynomial encoding, encryption and decryption in Clef. Use the scalar implementation as the baseline for later NTT, RNS and packed realizations. Schoolbook polynomial multiplication is slower but gives the initial proof and comparison target for later optimized Clef kernels.

For this one multiplication, the baseline can retain the three-component square and decrypt it using the declared degree-two secret-key polynomial. Addition pads the original two-component ciphertext with a zero third component. This removes relinearization/key-switch code from the first increment without weakening the selected security profile. Its component-count and decryption relation must be established for the chosen BFV specification before the path is admitted. Relinearization follows as a separately checked optimization. `Square` can return three components. OpenFHE's [documented distinction between multiplication without relinearization and relinearization](https://openfhe-development.readthedocs.io/en/latest/api/classlbcrypto_1_1CryptoContextImpl.html) describes that distinction.

BFV multiplication includes scaling and rounding, integer lifts and widened intermediates in addition to coefficient multiplication and reduction modulo `q`. Implement those from the selected specification, and prove their correspondence before connecting the encrypted `Square`.

The library defines the owned state and ciphertext types used by the callbacks. Platform supplies actual target arithmetic, memory and entropy premises; the library implements the cryptography. Existing library custody and proof contracts govern both the scalar baseline and optimized versions.

## Arithmetic and packed semantics

`src/Arithmetic.clef` defines the source expression and admission guard. Its maximum product is `10000`, maximum answer `10100`, and multiplicative depth one. The encrypted realization covers the accepted `Some` branch. Perform the clear guard on the client before encryption; a worker cannot inspect the hidden input to repeat that branch.

The first profile trusts the client's admission. Peer authentication does not prove an encrypted input lies in range. A hostile-client profile needs a checked range protocol or an explicit result contract over the larger modular domain.

For a batch of `m` admitted values, derive the elementwise map from that same source branch:

```text
slot i contains x[i]                  0 <= i < m <= S
slot i of the decrypted output = x[i] * x[i] + x[i]
remaining slots contain zero padding, which this circuit maps to zero
```

`S` is the selected backend's usable slot count. Slot order and active length are part of the encoding contract. This circuit needs no rotations, slot reductions or bootstrapping. Do not generate rotation keys. Keep different clients or key identities in separate ciphertexts; cross-key packing would require a different cryptographic protocol. After the synchronous scalar runs, pack `[0, 6, 100]`, expecting `[0, 42, 10100]`, then exercise partial and full batches.

Parallelism occurs at three levels:

| Scale | Unit | Selection |
| --- | --- | --- |
| Cryptographic SIMD | Many plaintext slots in one ciphertext | Fixed packing map derived from the batch shape |
| Machine SIMD | Polynomial coefficients in RNS limbs | Target-supported integer vector operations implementing admitted modular kernels |
| Service concurrency | Independent ciphertext jobs | Bounded workers and arenas chosen from memory and topology budgets |

Measure the costs at each level. Map plaintext slots and machine SIMD lanes explicitly. A packed operation executes polynomial arithmetic over the full ring.

Use [03: Tier 1-4 proof instrumentation](03-proof-instrumentation.md) for the proof obligations and [06: Implementation and acceptance](06-implementation-and-acceptance.md) for the landing sequence.
