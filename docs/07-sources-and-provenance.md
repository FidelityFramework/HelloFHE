# Sources and provenance

[Documentation index](README.md)

The synchronous design retrieved Composer revision `ee0e581706549ac1881b0dfd8015cb9221312dea`: [callback arguments, lines 1-15](https://forge.spkez.dev/FidelityFramework/Composer/src/commit/ee0e581706549ac1881b0dfd8015cb9221312dea/tests/NativeCallbacks/CallableArgumentComponents.clef#L1-L15) and [effectful calls, lines 1-100](https://forge.spkez.dev/FidelityFramework/Composer/src/commit/ee0e581706549ac1881b0dfd8015cb9221312dea/tests/NativeCallbacks/CallEffects.clef#L1-L100).

Indexed evidence used fresh/latest/equal snapshot `26be94971a5a2ab5dd24156c88ca6e331d6eb81ee1e81de0fb98118abab02416`, generation 25, with schema → find → pgq → sources. The [foundations source map](00-overview-and-foundations.md#existing-work-and-its-hellofhe-application) cites repository, revision, path and line span for the standing compiler/layout designs. The CPU/GPU designs were read in full; the BAREWire layout review covered lines 1-190 and actor substrate review lines 1-190.

Project and entry-point shape follow Composer revision `ee0e581706549ac1881b0dfd8015cb9221312dea`: [HelloWorld.fidproj, lines 1-18](https://forge.spkez.dev/FidelityFramework/Composer/src/commit/ee0e581706549ac1881b0dfd8015cb9221312dea/samples/console/FidelityHelloWorld/01_HelloWorldDirect/HelloWorld.fidproj#L1-L18) and [HelloWorld.fs, lines 1-13](https://forge.spkez.dev/FidelityFramework/Composer/src/commit/ee0e581706549ac1881b0dfd8015cb9221312dea/samples/console/FidelityHelloWorld/01_HelloWorldDirect/HelloWorld.fs#L1-L13). HelloFHE uses `.clef` filenames and a relative sibling platform dependency.

Cryptography foundation: Fidelity.Platform revision `8452124a5af4fb0d5d289d6f16ab47d23a07ed10`, `docs/CRYPTOGRAPHY_PROVIDERS.md` lines 1-29 and `Profiles/Linux_x86_64_Default/Fidelity.Platform.fidproj` lines 1-21, retrieved with the same pinned sequence. Fidelity.Cryptography is outside the published retrieval corpus; bounded direct reads used revision `ef4999df911a50480b23ca0f0b358ccdefefb0f8`: `README.md`, `src/README.md`, `src/Fidelity.Cryptography.Scaffold.fidproj`, `src/Core/Contracts.clef`, `src/Providers/Admission.clef`, and `docs/{architecture,algorithms,operation-contracts,platform-providers,key-custody-and-entropy,mbs-sealing}.md`.

The [FHE research index](../../arxiv-papers/research/FHE-modeling/README.md), [vision](../../arxiv-papers/research/FHE-modeling/00-vision.md) and [non-exploitability research](../../arxiv-papers/research/DBC/non-exploitability.md) retain their existing authority. The implementation and proof subjects are the first-party Clef sources. OpenFHE context/component handling and the parameter-guideline abstract were inspected on 2026-10-23. Use the selected scheme specification to define the algorithm contracts.


## Local OpenFHE checkouts

The three user-cloned projects and their exact revisions are registered in [08: OpenFHE study and attribution](08-openfhe-study-and-attribution.md#the-local-reference-set). That note records the inspected sources and how they inform original Clef work. The references remain outside the runtime and proof subjects of HelloFHE.
