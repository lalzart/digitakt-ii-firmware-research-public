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
| Recurring CPU-to-DSP state | `0x802`-byte payload in a `0xabc`-byte DSPI2/eDMA frame; 16 track-preserving unit records | Numeric clock rate, physical transaction capture and paired device occurrence |
| SRC machines | Seven public identities map to six SHARC selector roles; Oneshot, Werp and Slice have bounded public-to-role provenance | Complete machine algorithms and lawful live state |
| Project samples | Recorder save format, Project-slot indirection, CPU Rapid-GPIO endpoint, SHARC LP0/DMA30 receiver and first signed read | The external bridge, wire packing, paired occurrence and playback-scoped backing lifetime |
| DSP processing | Selector topology, shared buffers, coefficient schedules, ROOT gain stage and selected output-bank transform | Complete named effects, audibility, timing headroom and hardware behavior |
| Audio output | Selected 64-word banks, circular descriptor, DMA10 and SPORT4A ownership | Asynchronous fetch timing, codec/physical endpoint and sound |
| Overbridge | Digitakt II decoder and a bounded host shared-memory path | Exact host-to-device and host-to-firmware joins |

## The architecture at a glance

```text
ColdFire MAIN
  UI / project state / persistence / sequencing
       |
       | recurring 16-track state publication
       v
  DSPI2 + eDMA29/28 ---- physical link not captured ----> SHARC SPI2 RX
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

## The newest result: sample ownership rather than a hidden Flex machine

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

What is deliberately **not** claimed is just as important. The recorder buffer
and Project backing are not the same proved object; the external CPU-to-SHARC
bridge is still unidentified; and the retained static start path stops before
it can prove whether active playback pins Project backing. This rejects an
inspected candidate for an existing direct zero-copy route, not the possibility
of a future adapter or a simpler immutable temporary-sample workflow.

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

## Related independent work

Two Octatrack projects helped set the presentation standard for this snapshot:

- [OctaMax](https://github.com/mxldyn/octamax) publishes a broad architecture
  notebook, reverse-engineering tools and experimental patches.
- [OctaBam](https://github.com/sambanks/octabam) publishes original DSP56300
  effects alongside explicit build, hardware-test and resource status.

They target a different instrument, processor family and firmware lineage, so
their findings are not evidence for Digitakt II. The useful precedent here is
editorial: lead with what works or is mapped, show the architecture, and keep
the unproved boundary visible beside each result.

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
