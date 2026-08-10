# Architecture dossiers

These dossiers divide the recovered Digitakt II OS 1.15C architecture by
subsystem. Each statement remains bounded by the evidence language described
in the [research method](../research-method.md).

| Dossier | Primary question |
|---|---|
| [Boot, update, and images](boot-update-and-images.md) | How are the firmware domains represented, loaded, validated, and handed off? |
| [ColdFire runtime and objects](coldfire-runtime-and-objects.md) | What does the main processor own and how is project state represented? |
| [Publication and transport](publication-and-transport.md) | How does per-track state reach the SHARC receive image? |
| [SHARC runtime and dispatch](sharc-runtime-and-dispatch.md) | How does recurring DSP execution traverse units, lanes, and selector roles? |
| [DSP processing and state](dsp-processing-and-state.md) | Which bounded numerical and state contracts have been recovered? |
| [Output and DMA](output-and-dma.md) | How do selected output banks reach DMA and the audio peripheral? |
| [Host-software boundary](host-software-boundary.md) | What can be established inside Overbridge without claiming hardware provenance? |
| [Modification gates](modification-gates.md) | What offline modifications are proved, and which gates remain independent? |

These are concise synthesis documents, not firmware substitutes. Detailed
private evidence, raw tool output, disassembly, and machine-local provenance
are deliberately excluded from this public repository.
