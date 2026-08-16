# Digitakt II OS 1.15C architecture dossiers

These dossiers divide the accepted architecture by subsystem. Each statement
remains bounded by the evidence language described in the
[research method](../research-method.md); the documents summarize evidence but
do not create it.

| Dossier | Primary question |
|---|---|
| [Boot, update, and images](boot-update-and-images.md) | How are the firmware domains represented, loaded, validated, and handed off? |
| [ColdFire runtime and objects](coldfire-runtime-and-objects.md) | What does MAIN own and how is public/project state represented? |
| [Publication and transport](publication-and-transport.md) | How does recurring per-track state reach the SHARC completed image? |
| [Project sample-resource lifecycle](sample-resource-lifecycle.md) | How do recorded bytes become Project backing and reach the first SHARC sample read? |
| [SHARC runtime and dispatch](sharc-runtime-and-dispatch.md) | How does recurring DSP execution traverse units, lanes, and selector roles? |
| [DSP processing and state](dsp-processing-and-state.md) | Which bounded numerical and state contracts are actually recovered? |
| [Output and DMA](output-and-dma.md) | How do final banks reach DMA10/SPORT4A, and where does observation stop? |
| [Host-software boundary](host-software-boundary.md) | What is established inside Overbridge without claiming hardware provenance? |
| [Modification gates](modification-gates.md) | What offline modifications are proved, and which gates remain independent? |

These are concise synthesis documents, not firmware substitutes. Detailed
private evidence, raw tool output, disassembly, and machine-local provenance
are deliberately excluded from this public repository.

Accepted checkpoint: CPU-070, DSP-065, SYS-006 and LAB-005. Lifecycle state is
not duplicated here; the checkpoint identifies the evidence included in this
publication snapshot.
