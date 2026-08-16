# Digitakt II firmware research

**An independent, evidence-bounded map of the architecture visible in the
Digitakt II OS 1.15C update.**

The short version: the instrument is split between a ColdFire control side and
an ADSP-21569 SHARC audio side. We have recovered the recurring state-publication
path between them, the SHARC execution and output topology, and a separate
Project-sample resource path from saved audio through CPU-side backing and into
the first SHARC sample read.

This repository publishes the architecture and its limits. It does not contain
Elektron firmware, an installable patch, authentication material, or a claim
that an offline result is safe on hardware.

## What is mapped now

| Area | Recovered | Exact stop |
|---|---|---|
| Update and boot | SysEx/ELE3 layers, MAIN/updater/SHARC domains, validation and staged-slot rewrite | Reset-time updater selection, rollback and physical recovery |
| ColdFire control | Project/Sound objects, persistence, active-pattern transitions, recurring per-track publication | Runtime provider content, final ordering and many public meanings |
| Recurring inter-processor state | Full-duplex `0xabc`-byte DSPI2/SPI2 frame: CPU-to-DSP state plus paired DSP-to-CPU return buffers | Return-content meaning, numeric clock rate, physical capture and paired device occurrence |
| SRC machines | Seven public identities map to six SHARC selector roles; Oneshot, Werp and Slice have bounded public-to-role provenance | Complete machine algorithms and lawful live state |
| Project samples | Recorder save format, Project-slot indirection, CPU Rapid-GPIO endpoint, SHARC LP0/DMA30 receiver and first signed read | The external bridge, wire packing, paired occurrence and playback-scoped backing lifetime |
| DSP processing | Selector topology, shared buffers, coefficient schedules, ROOT gain stage and selected output-bank transform | Complete named effects, audibility, timing headroom and hardware behavior |
| Audio output | Selected 64-word banks, circular descriptor, DMA10 and SPORT4A ownership | Asynchronous fetch timing, codec/physical endpoint and sound |
| Overbridge | Digitakt II decoder and a bounded host shared-memory path | Exact host-to-device and host-to-firmware joins |

## The architecture at a glance

```text
ColdFire MAIN                                      SHARC
  UI / project state / persistence / sequencing
  DSPI2 SOUT + eDMA29 === recurring state ======> SPI2 RX
  DSPI2 SIN  + eDMA28 <=== paired return data ==== SPI2 TX
                                                   16 units / 32 lanes
                                                   six selector roles
                                                   shared P/B processing
                                                   scatter -> ROOT
                                                   selected output bank
                                                   DMA10 -> SPORT4A

Separate Project-sample path
  recorder service -> saved sample file -> Project slot/backing
  -> Rapid GPIO ---- external peer and packing unknown ----> LP0/DMA30
  -> sample-resource records/pages -> selected base -> first sample read
```

## Two inter-processor paths—and the remaining bridge problem

The current map contains two distinct ways for the processors to exchange
state:

1. **Recurring state exchange is full-duplex.** ColdFire sends a `0x802`-byte
   state payload inside a `0xabc`-byte DSPI2 frame while the SHARC sends paired
   buffers back during the same transaction. The transport geometry and
   selected CPU-to-DSP fields are mapped; the meaning of the DSP-to-CPU return
   content, the numeric bus rate and a captured hardware occurrence remain
   open.
2. **Project samples use a separate publication route.** The CPU endpoint is
   Rapid GPIO; the SHARC endpoint is LP0/DMA30. Both ends are mapped locally,
   but the external peer or PCB logic between them, wire packing and a paired
   transaction are not.

Closing the second join now needs new board-level evidence rather than more
ordinary firmware tracing: for example a schematic, netlist or layout, service
documentation, continuity or X-ray evidence, or safely accessible nodes whose
identity and electrical limits can be established. Existing board photographs
show the relevant balls disappearing beneath the BGA packages and do not expose
an attributable probe set.

## Sample ownership is a separate resource architecture

The sample campaign now reaches considerably farther than the original public
snapshot:

- recorder bytes cross a transient service aperture and are copied into a
  finalized sample file;
- that file has a recovered 64-byte header, byte-preserving payload path and
  deterministic mono/stereo lane handling;
- tracks retain a signed Project slot, while provider identity and backing live
  behind that slot;
- the CPU publishes five-word resource descriptors and 4 KiB pages through a
  Rapid-GPIO byte/strobe/ready protocol;
- the SHARC receives through LP0/DMA30, parses the resource update and reaches a
  selected signed 16-bit sample read.

The important architectural observation is the separation between capture
staging, finalized file representation, Project slot identity, Project backing
and SHARC resource state. These are not one proved shared sample buffer. The
external CPU-to-SHARC join is still unidentified, and the retained static start
path stops before it can prove whether active playback pins Project backing.

See [Project sample-resource lifecycle](docs/architecture/sample-resource-lifecycle.md).

## Target and research checkpoint

| | |
|---|---|
| Firmware | Digitakt II OS 1.15C |
| Official SysEx SHA-256 | `62d588456e47194bd56dfee9568fb9dd4521c4ff1e8b5427eb461355532e8c6c` |
| Accepted checkpoint | CPU-070, DSP-065, SYS-006 and LAB-005 |
| Hardware evidence | Authenticated board photographs only; no electrical capture, device execution or audible experiment |
| Publication snapshot | 2026-08-16 |

## Start here

- [Recovered architecture overview](docs/architecture-overview.md)
- [Architecture dossiers](docs/architecture/README.md)
- [Project sample-resource lifecycle](docs/architecture/sample-resource-lifecycle.md)
- [Research method and evidence boundaries](docs/research-method.md)
- [Bounded modification gates](docs/architecture/modification-gates.md)

## Repository layout

```text
README.md                         public entry point and current checkpoint
NOTICE.md                         publication and ownership notice
docs/architecture-overview.md     complete system map
docs/architecture/                subsystem dossiers
docs/research-method.md           evidence vocabulary and publication policy
```

## What this repository is—and is not

This is a compact research publication. It is useful for understanding the
instrument’s organization, selecting better questions and comparing evidence.
It is not a standalone reproduction kit or modified-firmware project.

The repository contains no:

- Elektron firmware or extracted images;
- installable or modified update packages;
- authentication keys, signing material or derived secrets;
- vendor register-definition packages;
- full disassemblies, string dumps or bulk firmware-derived text;
- private machine paths, hostnames, task ledgers or local tool output.

Offline instruction changes have executed under controlled simulation, but
modified-section re-encoding, complete package construction, recovery,
real-device execution, timing, audibility and safety remain independent and
unproved gates.

Digitakt, Digitakt II, Overbridge and Elektron are trademarks of their
respective owners. This project is independent and is not affiliated with or
endorsed by Elektron.
