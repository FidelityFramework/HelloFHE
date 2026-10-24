# Actor lifecycle and ciphertext storage

[Documentation index](README.md)

Optional service hosting, bounded admission, terminal outcomes and MBS persistence.

Olivier defines evaluator behavior, Prospero provides bounded supervision and placement, and Ariel handles execution and transport. Use their contracts with the selected native substrate.

The service state contains a validated public context, required evaluation material, circuit/artifact identity, bounded admission accounting and worker availability. Client key state remains in the client process. Each active evaluator owns its arena; read-only public tables may be shared only under the backend's concurrency contract.

One actor job contains one ciphertext batch. Batch on the client under one key and encoding; the worker schedules those jobs without decrypting or repacking their hidden slots. Initial scheduling is FIFO within a session and bounded fair selection across sessions. Coexponential completion uses a finite admitted batch, fair scheduling and total correctness of the whole continuation, including native completion and reply handling. Do not let a worker retain an arena while synchronously waiting for another evaluator; callbacks re-enter through the owned completion path.

## Envelope and admission

Extend `src/Protocol.clef` when connecting transport; it currently records only invocation and payload. The binding must carry or resolve the following fields without treating them as proof tags inside the ciphertext:

| Public field | Admission rule |
| --- | --- |
| Protocol/codec version | Select one supported bounded decoding contract |
| Session epoch and key-set/context identity | Resolve an already admitted context; reject stale or cross-context messages |
| Circuit/artifact and parameter-profile identity | Match the accepted source realization and resource plan |
| Invocation identity and request digest | Match one pending completion; reject identity reuse with different request content |
| Active lane count and payload extent | Fit the packing plan and byte limits; bound expanded decode size too |
| Outcome and optional public error class | Exactly one local terminal selection; no plaintext or secret-bearing diagnostics |

Use authenticated transport or authenticated framing to bind these fields and the ciphertext to the intended peer. FHE is malleable; ciphertext and correlation alone do not authenticate a sender or prove an evaluated result. An artifact identity selects the agreed computation but is not a per-answer execution proof.

Before deserialization, reserve the admitted worst-case expanded job bytes, scratch and result capacity as well as a job slot. Cap context/key installation separately. When credits are exhausted, return a bounded busy outcome or apply transport backpressure before accepting more payload. Do not use an unbounded mailbox or a diagnostic pending-count value as the admission mechanism.

## State transitions

| Current state and event | Action | Resource and reply rule |
| --- | --- | --- |
| Unadmitted, invalid framing/context | Reject before evaluation | Release bounded transport staging; no evaluator arena acquired |
| Admitted, queued | Record epoch, invocation and request identity | Hold accounted bytes and one pending reply authority |
| Queued, worker available | Transfer request ownership and launch | Worker owns input, scratch and output reservation |
| Running, native/device completion | Validate completion identity and construct result or failure | Select terminal outcome once; arena remains live through every native reference |
| Terminal selected, transport publish | Send or record bounded delivery failure | Release payload after transport consumption; release credits at actual last use |
| Queued, cancellation | Remove queued job atomically | Select cancelled once and retire its reservation |
| Running, cancellation/timeout | Record request to cancel; stop only at a supported safe boundary | Local timeout does not free live native buffers; eventual completion is consumed or discarded through the same terminal gate |
| Draining, stop | Refuse new admission and finish/cancel owned work | Retire arenas and public context only after completions have settled |
| Restart or crashed session | Advance epoch; resolve outstanding attempts as unknown/failed according to the binding | Reject stale completions; reclaim only through process termination or confirmed native completion |

At-most-once local terminal selection does not imply exactly-once remote execution or reply delivery. Keep a bounded completion ledger until the admitted acknowledgment/retention boundary. Retrying after an ambiguous crash creates a fresh attempt identity; do not promise deduplication after ledger expiry. Late replies cannot consume a new attempt's authority.

Immediate disposal follows the justified last use, including native callbacks and transport consumption. Active marshaling makes that ownership transfer explicit. Zero-copy is selected only when the accepted representation, aliasing and lifetime contracts permit it; a copy can improve locality and simplify retirement.

## Ciphertext persistence with MBS

Use MBS as ciphertext storage, keyed by context/key-set, circuit/profile identity, codec version and ciphertext digest. Recovery validates those identities before loading an object for evaluation. Keep secret keys and plaintext staging out of the worker store. Bound the in-memory working set and account for fetch/decode cost when scheduling.

A durable job/result record can extend retry handling only with an explicit atomic commit and replay policy; blob persistence alone does not supply exactly-once execution. Separate dropping a cache reference, retiring a blob and erasing a client secret. Worker restart must preserve the client custody boundary and reject objects from an incompatible context.
