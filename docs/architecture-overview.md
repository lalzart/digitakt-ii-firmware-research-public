# Digitakt II OS 1.15C — recovered architecture

This is the public synthesis of the accepted architecture through CPU-070,
DSP-065, SYS-006 and LAB-005. It describes what the studied bytes, bounded
simulator executions and supplied board photographs establish—and where each
line of evidence stops.

Target SysEx SHA-256:
`62d588456e47194bd56dfee9568fb9dd4521c4ff1e8b5427eb461355532e8c6c`.

## Cross-system map

```text
Official OS 1.15C SysEx
  -> ELE3 container
       |-- section 3: ColdFire MAIN @ 0x40000400
       |-- section 4: updater @ 0x80000400
       `-- section 7: ADSP-21569 SHARC loader/application records

ColdFire MAIN
  UI / persistence / Project and Sound objects / sequencing
  -> recurring per-track transform
  -> 0x802-byte payload in a 0xabc-byte DSPI2/eDMA frame
  -> 16 unit records, one per track

SHARC
  SPI2 completed-image receive
  -> 16 units / 32 lane ordinals
  -> seven numeric identities / six selector roles
  -> role-exclusive and shared P/B processing
  -> scatter and six-vector merge
  -> ROOT0 / ROOT1
  -> selected 64-word output bank
  -> circular descriptor -> DMA10 -> SPORT4A

Separate Project-sample route
  recorder service -> finalized sample -> Project slot/backing
  -> Rapid GPIO -/-> unknown external bridge -/-> LP0/DMA30
  -> SHARC resource records/pages -> selected base -> first sample read

Overbridge
  Digitakt II decoder -> host shared memory -> application ring
  -/-> firmware and physical-device joins remain unproved
```

## 1. Images, validation and boot

The authenticated update is a 1,708,064-byte SysEx stream containing a
1,347,728-byte decoded ELE3 container. ColdFire MAIN is big-endian. SHARC
content is represented by ADSP-2156x loader/application records with separately
reconstructed program and data spaces.

MAIN validates a content checksum, minimum version and cryptographic trailer
before destructive erase/program work on the staged ELE3 slot beginning at
nonvolatile offset `0x80000`. The selected writer repeats validation before its
first erase and requests reset after programming.

The packaged updater can read section 3 from that slot, decompress it to
`0x40000400` and call the MAIN entry. What happens earlier is not closed: the
reset-time component that selects, places and enters the updater—and any
fallback after a partial or invalid slot—remains unobserved. Recovery is
therefore `H-OPAQUE`, not proved independent, dependent, safe or unsafe.

On SHARC, 104 represented loader records and the operational FreeRTOS/Audio
Task startup route are mapped. The declared-entry/private-MMR/vector handoff
into that route remains opaque. Measured gaps are not promoted to code caves;
zero additional reusable bytes are proved.

## 2. ColdFire objects and recurring state publication

The ColdFire side owns user-facing objects, persistence and the recurring
construction of track state. Sixteen track records are transformed into a
`0x802`-byte payload inside a padded `0xabc`-byte full-duplex frame.

The publisher stages the previous transmit buffer before rebuilding the next
one. A level-6 eDMA-completion path software-forces a level-5 publication
interrupt. Static ordering is exact locally, but complete physical completion,
race freedom and one captured device occurrence are not proved.

The transport contract is unusually specific:

- DSPI2 CTAR0, SPI mode 1;
- 16-bit, MSB-first words on active-low PCS0;
- one continuous `0x55e`-word transaction;
- eDMA29 transmit and eDMA28 receive;
- PF6/PF5/PF4/PF3 muxed to SCK/PCS0/SIN/SOUT;
- symbolic `fSCK=fBUS/3=fSYS/6`.

The retained firmware does not supply the reset/reference clock configuration
needed for a numeric bus rate. No analyzer adequacy or safe attachment claim
follows from the symbolic timing.

## 3. SHARC execution and selector roles

The recurring operational path covers Audio Task notification, root
`0x001c74cd`, main processor `0x001c2b24`, per-unit/lane traversal, common
processing, output copy and return. Sixteen units each own two lanes, preserving
CPU track ordinal as `q=2*n+ell` across 32 lane ordinals.

Lawful numeric identities map as follows:

| Identity | Selector role | Target |
|---:|---:|---:|
| 0 | 0 | `0x001c65bd` |
| 1 | 1 | `0x001c6715` |
| 2 | 2 | `0x001c6782` |
| 3 | 3 | `0x001c686e` |
| 4 | 4 | `0x001c692f` |
| 5 | 0 | `0x001c65bd` |
| 6 | 5 | `0x001c6992` |

All six roles reach first common reconvergence at `0x001c65fe`. The retained
topology separates role-exclusive, subset-shared and common work. Public
Oneshot/Werp exports establish a bounded conditional identity-0/1 route to
roles 0/1. A version differential joins Slice’s introduction to identity 6 and
role 5. Those joins do not identify complete algorithms, and identity 5’s reuse
of role 0 demonstrates why “one role equals one machine” would be wrong.

## 4. Public controls and shared coordinates

The six documented SRC machines have machine-specific parameter schemas laid
over shared logical slots. Exact persistence/publication work reaches farther
for selected controls:

- WERP Segment Size reaches shared logical slot `0x1f` conditionally;
- WERP Mode reaches slot `0x20`, followed by an authenticated last-match-wins
  generic adjustment whose occurrence-time inputs remain opaque;
- Grid SAMP, SLICE, LEN, GRID, LEV, TUNE and PLAY have a persistence atlas;
- Grid TUNE conditionally matches the slot-`0x19` width/scaling route, but a
  same-word writer prevents public-value survival from closing;
- Slice adds a nine-row schema, identity 6 and role-5 preparation while sharing
  bounded ancestry with an earlier Grid-related helper.

Slot reuse is an implementation technique, not proof of shared public meaning.
Selected-live object order remains open for the exported WERP/Grid examples.

## 5. Project samples and backing lifetime

The separate sample-resource investigation now closes each local stage from a
recording response through the first SHARC read:

- the save writer creates a 64-byte header, byte-preserving payload and 16-byte
  trailer;
- header fields distinguish mono/stereo, payload byte count and 48 kHz;
- stereo frames are split deterministically into two 16-bit-word lanes;
- a track retains a signed Project slot at receiver `+0x4c`, not a raw backing
  pointer;
- provider admission may keep, reuse or load backing behind that slot;
- five-word descriptors and 4 KiB pages reach the ColdFire Rapid-GPIO endpoint;
- LP0_RX/DMA30/source `0x51` supply the SHARC event-4 receiver;
- event 4 updates the resource table/pages and reaches a selected signed 16-bit
  first sample read.

The CPU and SHARC endpoints do not yet meet in one authenticated physical
transaction. Their direction and geometry are compatible, but the peer, wiring,
dword packing and paired occurrence remain `INFERENCE` rather than
`CROSS-DOMAIN`.

Backing lifetime also remains deliberately split. CPU-local analysis permits a
higher-priority final-owner removal to make a backing range reusable before a
later invalidation reaches the publication mutex. A separate type-`0x0c`
playback-start route closes scheduler-local refcounts but stops before it can
join that start identity to the receiver carrying selected slot S. Neither a
playback backing pin nor its absence is proved.

See the dedicated
[Project sample-resource lifecycle](architecture/sample-resource-lifecycle.md).

## 6. Bounded DSP mechanics

The recovered DSP work is best described as exact local contracts rather than
named effects:

- a 32-position coefficient schedule merges six work-vector families through
  three A/B pairs and auxiliary XA/XB inputs;
- a coefficient reader performs a bounded phase-indexed calculation;
- ROOT is strongly classified as a scalar gain-smoothing stage within the
  accepted screen;
- selected shared-B helpers and loops have exact equations and finite
  positive/negative controlled-response screens;
- logical slot `0x1f` reaches common scaled lane/table/frame state;
- controlled slot `0x19` reaches a normalized/clamped table-index and adjacent
  table-word blend;
- controlled slot `0x1a` reaches scalar form `x-2*x*x`;
- candidate generator gates are zero across the frozen initialized universe,
  while a runtime-generated or external nonzero producer remains possible.

Recognizable arithmetic does not independently establish pitch, filtering,
resampling, an effect name or audible behavior.

## 7. Output ownership

ROOT selects one of two 64-word banks at
`0x00261cb8..0x00261db4` and `0x00261db8..0x00261eb4`. Authentic code negates
every selected word in place. A corrected offline fixture proves that a
same-width three-byte instruction replacement produces pass-through while
pristine and sham controls retain negation.

The post-transform bank belongs structurally to a two-bank circular descriptor.
L2 code installs its alias in `DMA10_DSCPTR_NXT=0x31023000` and configures
SPORT4 half A for transmit. The simulator does not emit the asynchronous
descriptor fetch, so fetch timing, codec/connector identity, cadence,
utilization, audibility and safety remain unobserved.

## 8. Host-software boundary

Hash-bound Overbridge packages contain a reachable Digitakt II-specific
80-byte inbound decoder, a host shared-memory producer/consumer path and 12
strict read-only frame observations. The selected DriverKit handler and shared
writer stop at indirect ownership boundaries.

This is host-software evidence. It does not establish byte-for-byte correlation
with a firmware field, a deterministic device fixture or physical USB behavior.

## Physical evidence

Supplied top-face photographs identify the ColdFire package as MCF54415 and the
SHARC as ADSP-21569. The declared Rapid-GPIO and LP0 signal balls disappear
beneath their BGA packages, visible fanout stops at vias/hidden layers, and no
inventoried exposed point has a documented transport identity.

That is a bounded result from those photographs, not a universal statement
that no trace can be probed. A useful next node map would require a schematic,
netlist/layout, service documentation, continuity work or X-ray evidence under
a separately reviewed hardware protocol.

## Modification boundary

Two offline changes have bounded evidence:

- a size-preserving selector reroute under repeated simulator controls; and
- a same-width selected-bank transform replacement changing three stored bytes
  and four Hamming bits.

Static compatibility, simulator execution and named regression watches do not
close modified-section re-encoding, complete ELE3/SysEx packaging, recovery,
hardware execution, timing, audibility or safety. No installable image is
published or implied.

## Stable limits

- Public names identify bounded routes, not complete algorithms.
- Controlled simulator values are not necessarily lawful device state.
- Finite numerical screens are not universal mathematical models.
- Local CPU and SHARC endpoints are not a physical join without the external
  bridge and occurrence.
- DMA/SPORT configuration is not an observed asynchronous fetch or audio proof.
- The sample route does not establish a dormant Flex machine, zero-copy backing
  ownership or safe live replacement.
- Static update code does not prove physical recovery after a failed write.

## Subsystem dossiers

- [Boot, update, and images](architecture/boot-update-and-images.md)
- [ColdFire runtime and objects](architecture/coldfire-runtime-and-objects.md)
- [Publication and transport](architecture/publication-and-transport.md)
- [Project sample-resource lifecycle](architecture/sample-resource-lifecycle.md)
- [SHARC runtime and dispatch](architecture/sharc-runtime-and-dispatch.md)
- [DSP processing and state](architecture/dsp-processing-and-state.md)
- [Output and DMA](architecture/output-and-dma.md)
- [Host-software boundary](architecture/host-software-boundary.md)
- [Modification gates](architecture/modification-gates.md)
