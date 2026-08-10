# Digitakt II OS 1.15C — recovered architecture

This document summarizes the accepted, bounded architecture recovered from the
studied image. Detailed synthesis is organized in the
[architecture dossiers](architecture/README.md). The private evidence corpus,
task ledgers, and raw tool output are deliberately not included here.

## Cross-system architecture

```text
ColdFire MAIN
  boot/update and user-facing object system
  -> persistence, project/Sound state, tasks/callbacks
  -> per-track transform and recurring publication
  -> 0x802-byte payload in a 0xabc-byte DSPI/eDMA frame

SHARC
  loader/application records and FreeRTOS startup
  -> completed SPI2 RX image
  -> 16 units / 32 lane ordinals
  -> six internal selector roles and P/B processing
  -> scatter -> ROOT -> selected 64-word bank
  -> circular descriptor -> DMA10 -> SPORT4A

Overbridge host software
  Digitakt II decoder -> host shared memory -> app ring
  -/-> exact firmware and physical-hardware joins remain unproved
```

## Subsystem dossiers

- [Boot, update, and images](architecture/boot-update-and-images.md)
- [ColdFire runtime and objects](architecture/coldfire-runtime-and-objects.md)
- [Publication and transport](architecture/publication-and-transport.md)
- [SHARC runtime and dispatch](architecture/sharc-runtime-and-dispatch.md)
- [DSP processing and state](architecture/dsp-processing-and-state.md)
- [Output and DMA](architecture/output-and-dma.md)
- [Host-software boundary](architecture/host-software-boundary.md)
- [Modification gates](architecture/modification-gates.md)

## Accepted milestone

Synthesis is accepted through CPU-063, DSP-061, SYS-006, and LAB-004. Major
bounded results include:

- exact ColdFire image, object, publication, transport, and M8 campaign slices;
- an exact eight-edge active-pattern transition graph across live, pending,
  previous, and target pointers; PatternSelect and PatternChain both reach its
  queue input; twelve downstream reader families are bounded, and a registered
  source-44 handler conditionally reaches `LIVE+0x1db79`, while target/provider
  content, gate/occurrence, public meaning, and all step/trig/p-lock behavior
  remain open; the source-57 successor adds ten phases and 21 finite loops,
  with its first 16-record state loop still downstream of provider opacity;
- a bounded CPU sampling lifetime map in which one chosen path is copied into
  sibling work/completion closures; after file write/finalize/close, fixed
  completion constructs `AssignPrompt` with the same path and an independent
  Project slot, and its later track-key route reaches Project RAM plus exactly
  two track-writer targets; this supersedes the former `SampleListView` owner/
  nonjoin but proves no PCM-backing or runtime-occurrence identity;
- the recurring DSPI2/eDMA transport profile: CTAR0 mode 1, 16-bit MSB-first,
  PCS0, 1,374 words, eDMA29 TX/eDMA28 RX, symbolic `fSCK=fBUS/3`, and exact
  port-mux roles; retained MAIN/updater code inherits the clock, so numeric
  timing terminates at reset/reference configuration before updater entry,
  without analyzer, PCB, voltage, or hardware claims;
- a controlled Track-1 Oneshot/Werp export bridge to persisted identities 0/1
  and conditional selector roles 0/1, plus a controlled exact role-1 scalar/
  frame-preparation path to first common; four residues die before read and
  untouched `R3/R6` stop at an indirect target, without live-object,
  downstream, or complete-algorithm closure;
- a six-machine public-manual-to-authentic-descriptor concordance showing
  machine-specific parameter schemas over shared logical slots, with only a
  conditional slot-`0x1f` SHARC coordinate and no complete algorithm claim;
- a controlled same-WERP SEG bridge from persisted current-record
  `+0x6a..+0x6b` through `Sound+0x52` and slot `0x1f` to the conditional common
  SHARC reader; static selected-live occurrence stops at manager-owned project
  content/order and is neither proved nor excluded;
- a controlled same-WERP MODE bridge from record `+0x6c..+0x6d` through
  `Sound+0x54` and slot `0x20` to conditional packet `+0x0e8+n*0x60`; the
  later adjustment's exact decision contract and all 24 descriptor/six
  coefficient roots close for one occurrence; no retained source supplies an
  atomic ColdFire capture, so final value and every DSP meaning remain open;
- a seven-control Grid persistence atlas covering SAMP, SLICE, LEN, GRID, LEV,
  TUNE, and PLAY, with two unresolved SAMP auxiliary bytes; TUNE conditionally
  matches snapshot `+0xda` and the slot-`0x19` scaling, but a same-word writer
  prevents public-value survival and `CROSS-DOMAIN` closure;
- one common slot-`0x1f` preprocessing and post-role consumer chain across all
  six accepted zero-valued role paths; controlled nonzero words alter common
  lane/table/frame state identically for matched roles 1 and 4 through the
  bounded two-block screen; an authentic later gated `-1` setter exists, but
  accepted schedules keep its gate zero and both control paths reconverge; the
  selected raw signed-zero state returns in `F0` and dies at first caller read
  under accepted `F2=+0.0`;
- two controlled all-role candidate primitives: slot `0x19` reaches one
  normalized/clamped table-index path and slot `0x1a` reaches one frame scalar
  `x-2*x*x`; slot `0x19` continues through an adjacent-word blend whose
  accepted integer controls leave the upper word zero-weighted and state buffer
  unread before overwrite; the conditional TUNE coordinate does not establish
  public-value survival, lawful fractional reach, or TUNE/PLAY semantics;
- an exact pre-Slice/1.15 version boundary adding the nine-row Slice schema,
  public identity `6`, and role 5 over renumbered descriptor namespaces and
  pre-existing Grid-related helper ancestry, without complete-algorithm reach;
- a neutral role-5 equation map separating inherited clamp/scale, packing,
  gate, and conditional-state mechanics from Slice-era endpoint/state/packing
  additions; all eight accepted windows take only the zero-gate restore path;
- a five-plus-one candidate-generator split after selector reconvergence; both
  controlled candidates share an unresolved pointer and zero gate before any
  authenticated reader, rejecting one all-six executed reader lineage in that
  screen without rejecting broader shared modular machinery; both gate formulas
  join one byte, direct lifecycle writers are zero-only, and role 2's positive
  arm resets that byte only after reading it; the frozen initialized universe
  contains no nonzero pre-read writer, leaving runtime-generated or
  uninitialized external owner/content as the first remaining source boundary;
- a bounded `H-OPAQUE` update/recovery envelope;
- SHARC loader/runtime and resource ownership with zero proved reusable bytes;
- complete retained selector topology and the shared buffer graph;
- the exact six-vector coefficient-scheduled per-word merge equation, its
  direct C/D/E frontier with no F0/F1 join before return, and a controlled
  bounded linear/matrix-style screen, without public meaning;
- an exact controlled coefficient equation and bounded phase-indexed structure;
- an exact ROOT equation and strong bounded scalar gain-smoother classification;
- exact selected shared-B helper/loop mechanics and finite controlled
  sign-symmetric word-local response screens, plus a bounded zero-guard
  `PASS`/`SV` activation screen;
- selected-bank output ownership through DMA10/SPORT4A configuration;
- host-only Overbridge decoder/shared-memory paths without hardware claims;
- one offline selector reroute and one corrected, executed same-width output
  arithmetic replacement.

## Stable evidence limits

- Public names identify only bounded routes: Oneshot/Werp to roles 0/1 and
  Slice identity `6` to role 5; they do not identify complete algorithms.
- Controlled simulator state is not lawful device state.
- Finite zero/nonzero screens are not universal numerical models.
- DMA/SPORT configuration is not an observed asynchronous fetch or physical
  endpoint.
- Host software is not firmware or hardware evidence without an exact join.
- Static/simulator modification does not prove re-encoding, packaging,
  recovery, device execution, audibility, or safety.
- The sampling result proves static saved-path continuity, not successful
  runtime occurrence, PCM backing ownership, Project-RAM backing identity, DSP
  publication, or a Flex-like feature path.

## Modification boundary

The architecture-readiness gate is satisfied and can name three neutral
operations: the coefficient reader, ROOT gain smoother, and selected-bank sign
transform. This is a decision point only. No target, package, deployment, or
hardware action is selected or authorized.
