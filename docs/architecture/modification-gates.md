# Bounded modification evidence and gates

## Proved offline modifications

### Selector reroute

DSP-019 proves one byte-exact, size-preserving reroute of an existing selector
decision in repeated offline simulator controls. The expected local state
changes and declared downstream/unrelated watches remain stable.

### Output transform

DSP-020 establishes a same-width replacement and arithmetic oracle for the
selected-bank sign transform. DSP-021 rejects its original fixture placement:
the pointer restore ran inside the hardware loop and the target was `0x3b00`
below the selected bank.

DSP-022 repairs the fixture endpoint with one displacement-byte change. DSP-023
then proves pristine/sham 64-word negation versus patched pass-through under the
corrected controlled matrix, persisting through declared checkpoints.

DSP-024 closes the inner image/layout boundary: 24 existing instruction bytes
are reused, exactly 3 bytes/4 bits change, and zero additional usable code/data
bytes are proved. Authentic SysEx/ELE3 coordinates and integrity relations are
mapped.

## Independent gates

| Gate | Current bounded disposition |
|---|---|
| Static instruction compatibility | accepted for the exact two modifications |
| Simulator execution | accepted only for declared controlled matrices |
| Regression controls | accepted only for the named watches and schedules |
| Modified section re-encoding | unproved |
| Complete container/package compatibility | unproved |
| Recovery/rollback | bounded `H-OPAQUE`; no physical recovery claim |
| Hardware execution and timing | absent |
| Audibility and public behavior | absent |
| Safety and deployment | unauthorized and unproved |

No task, coverage status, semantic assertion, or modification-index entry
authorizes flashing, signing, transmitting, installing, or communicating with
hardware.

## Decision point

The architecture-readiness gate can name three neutral operations with exact
local contracts: the coefficient reader, ROOT gain smoother, and selected-bank
sign transform. This enables a separate target-ranking discussion; it does not
select a target or start modification work.
