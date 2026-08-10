# Digitakt II firmware research

An independent, documentation-only study of the architecture visible in the
Digitakt II OS 1.15C update.

The practical result so far is a bounded map of the instrument's ColdFire
control side, SHARC DSP side, state publication path, and final audio-output
handoff. It is not source code for the Digitakt II and it is not a modified
firmware release.

## Start here

- [Recovered architecture overview](docs/architecture-overview.md)
- [Architecture dossiers](docs/architecture/README.md)
- [Research method and evidence boundaries](docs/research-method.md)

## What we have learned

- The update is not simply an unreadable encrypted blob. Its main processor
  and DSP regions can be identified and studied after local extraction.
- The ColdFire side owns boot/update handling, user-facing state, persistence,
  and the recurring publication of per-track data.
- The SHARC side receives that data, dispatches recurring DSP work, and moves a
  selected output bank toward DMA and the audio peripheral.
- Small, controlled offline changes can be made to decoded DSP instructions
  and observed in a simulator.
- That does **not** mean a modified update can be installed. Recompression,
  layout, checksums, authenticated packaging, recovery behavior, real-device
  execution, and audibility are separate gates.
- Hardware probing is still needed to confirm the physical DSP link and move
  from static architecture to measured device behavior.

## Deliberate exclusions

This repository contains no:

- Elektron firmware or extracted firmware images;
- installable or modified update packages;
- authentication keys, signing material, or derived secrets;
- vendor register-definition packages;
- full disassemblies, string dumps, or other bulk firmware-derived text;
- private machine paths, hostnames, research ledgers, or local tool output.

The documents are compact research summaries. The private evidence corpus is
not published here, so this repository should not be treated as a standalone
bit-for-bit reproduction kit.

## Scope and safety

The findings are specific to the studied OS 1.15C image unless a document says
otherwise. Static analysis and simulation do not establish safe flashing,
hardware execution, or audible results. Nothing here is an instruction to
bypass update authentication or install altered firmware.

Digitakt, Digitakt II, Overbridge, and Elektron are trademarks of their
respective owners. This project is independent and is not affiliated with or
endorsed by Elektron.
