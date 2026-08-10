# Research method and evidence boundaries

This work separates what the bytes prove from what a simulator suggests and
what only a real instrument could establish.

## General workflow

1. Hash and preserve a locally obtained update without altering the original.
2. Extract working copies locally and identify the main processor, DSP, and
   loader regions.
3. Use static analysis to map code, data, callers, writers, and ownership.
4. Export only small, question-specific slices into a controlled evidence
   record.
5. Use a simulator for bounded tests where the relevant inputs and outputs can
   be controlled.
6. Record the earliest point where content, ownership, order, or physical
   behavior becomes opaque.

The public repository begins after those steps: it contains the resulting
compact synthesis, not the firmware or private evidence workspace.

## Evidence language

| Term | Meaning |
|---|---|
| `STATIC-AUTH` | A relationship supported by authenticated bytes and static analysis. |
| `SIM-CTRL` | A result observed under explicitly controlled simulator state. |
| `NEGATIVE-BOUNDED` | Something was not found within a named, finite search boundary. |
| `INFERENCE` | A useful interpretation that is not directly proved by the bytes. |
| hardware evidence | A result measured on the physical instrument with a documented setup. |

One class does not automatically promote into another. For example, an
instruction replacement executing in a simulator does not prove that the
device will accept a repackaged update, execute it safely, or produce the
expected sound.

## Modification boundary

Changing a decoded instruction is only the first layer. A device-accepted
update may also depend on compression, exact layout, checksums, authenticated
packaging, version/recovery policy, and other validation performed outside the
edited region. Those gates are intentionally treated separately.

No authentication secret or accepted modified update is published here. The
current hardware frontier is measurement of the physical processor-to-DSP path
and controlled comparison against the static model.

## Publication policy

Public summaries should remain useful without becoming substitutes for vendor
firmware. They therefore exclude full images, decoded binaries, full
disassemblies, bulk string dumps, authentication material, vendor-restricted
inputs, and machine-local provenance. Claims retain explicit limits even when
that makes the conclusion less dramatic.
