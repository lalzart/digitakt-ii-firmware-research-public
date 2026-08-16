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
| `SIM-AUTH` | Authentic instructions executed in the simulator with accepted live-ins. |
| `SIM-CTRL` | A result observed under explicitly controlled simulator state. |
| `HOST-PUBLISH` | A ColdFire producer-to-packet dataflow is proved inside the firmware. |
| `CROSS-DOMAIN` | The same field or ordinal is proved across a ColdFire publication and a SHARC consumer. |
| `HARDWARE` | Evidence obtained from the physical instrument, with the exact observation method stated. |
| `NEGATIVE-BOUNDED` | Something was not found within a named, finite search boundary. |
| `INFERENCE` | A useful interpretation that is not directly proved by the bytes. |

One class does not automatically promote into another. For example, an
instruction replacement executing in a simulator does not prove that the
device will accept a repackaged update, execute it safely, or produce the
expected sound.

## Modification boundary

Changing a decoded instruction is only the first layer. A device-accepted
update may also depend on compression, exact layout, checksums, authenticated
packaging, version/recovery policy, and other validation performed outside the
edited region. Those gates are intentionally treated separately.

No authentication secret or accepted modified update is published here.
Authenticated board photographs establish package identities and a bounded
negative probe screen, but no electrical transaction has been captured. The
current hardware frontier is still measurement of the physical
processor-to-DSP paths and controlled comparison against the static model.

## Claim shape

A useful public claim should say four things:

1. the exact firmware version or host package it concerns;
2. the evidence class and confidence;
3. the smallest relationship that was actually closed; and
4. the earliest remaining opacity or a concrete falsifier.

Addresses, nearby strings, table sizes, zero defaults and recognizable
arithmetic are routing evidence. None of them independently names a public
machine, effect or audible behavior.

## Publication policy

Public summaries should remain useful without becoming substitutes for vendor
firmware. They therefore exclude full images, decoded binaries, full
disassemblies, bulk string dumps, authentication material, vendor-restricted
inputs, and machine-local provenance. Claims retain explicit limits even when
that makes the conclusion less dramatic.
