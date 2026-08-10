# Boot, update, and image architecture

## Evidence boundary

The accepted model is offline. Hash-bound authentic bytes, retained listings,
official decoder/tool outputs, and bounded simulator controls support it. It
does not establish secure-boot policy, private keys, installability, physical
recovery, hardware timing, or safe deployment.

## Image domains

- ColdFire MAIN is a big-endian executable domain loaded around `0x40000400`.
- SHARC content is represented by ADSP-2156x loader/application records with
  separately reconstructed code/data spaces.
- Official ADI decoding and retained manifests ground the SHARC reconstruction;
  ColdFire conclusions are checked against authentic bytes because unsupported
  instructions can degrade decompiler output.

## ColdFire update path

MAIN validates checksum, minimum-version, and cryptographic-trailer relations
before erase/program work on the staged ELE3 slot beginning at nonvolatile
offset `0x80000`. The selected writer repeats validation, programs the staged
slot, and requests reset. Within the packaged updater, section 3 can be read,
decompressed to `0x40000400`, and handed to the authentic MAIN entry.

The updater copy located in the rewritten ELE3 slot does not prove that this is
the instance selected after reset. The earliest exact opacity is the earlier
reset-to-updater selection/load/entry component and fallback reachability.
Recovery is therefore `H-OPAQUE`, not proved independent or dependent.

## SHARC loader and startup

The bounded loader/resource inventory covers 104 represented records, both
declared entries, record-67 vector decode, and a separate operational chain
through FreeRTOS, Audio Task creation, notification waiting, and the recurring
block loop. The declared-entry/private-MMR/vector/boot-kernel handoff to that
operational startup remains opaque.

Seven represented gaps total 4,855,924 bytes. They are candidate static slack,
not reusable capacity: stack/heap, aliases/external memory, DMA-time ownership,
and preserved-layout constraints do not jointly close. Zero bytes are presently
proved reusable.

The bounded simulator cycle screen cannot be converted to utilization because
configured core frequency, authentic wake cadence, hardware-cycle equivalence,
deadline margin, and physical safety are unknown.
