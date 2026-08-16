# ColdFire publication and SHARC transport

## Packet contract

The recurring state payload is `0x802` bytes inside a padded `0xabc`-byte
full-duplex DSPI/eDMA frame. Sixteen `0x60`-byte unit records preserve track
ordinal from ColdFire track `n` to SHARC unit `n`.

The ColdFire publication path stages the previous transmit buffer before
rebuilding the next state packet. It is tied to a level-5 interrupt that is
software-forced from a level-6 eDMA-completion path. The bounded static model
does not prove full hardware completion, exact physical ordering, or race
freedom.

## SHARC receive boundary

SPI2 RX PDMA uses completed-image buffering. The recurring SHARC code selects a
completed local snapshot only after receive completion. Each unit owns two lane
ordinals, giving `q=k=2*n+ell` over 16 units and 32 lanes.

Exact cross-domain cases include public numeric source identity and the bounded
M8 scalar/source coordinate chain. A mechanical field join does not prove that
the value is lawful in all runtime states or establish public meaning.

CPU-057 closes the programmed physical-link contract without observing the
link. The recurring frame selects DSPI2 CTAR0, SPI mode 1, 16-bit MSB-first,
active-low PCS0, and one continuous `0x55e`-word window. TFFF drives eDMA29 TX;
RFDF drives eDMA28 RX. CTAR0 gives `fSCK=fBUS/3=fSYS/6` and
`tCSC=tASC=tDT=2/fBUS`. PF6/PF5/PF4/PF3 mux to
SCK/PCS0/SIN/SOUT. Runtime clock state, numeric SCK/frame duration, installed
package/ball, EVDD, PCB net/test point, recurrence cadence, electrical safety,
and hardware behavior remain unproved. The analyzer sampling margin is
`INFERENCE`, not a device requirement.

CPU-058 closes the declared retained-image clock search negatively. Complete
MAIN/updater direct, raw-address, and constant-propagation screens find no
viable reference/PLL/divider selection before DSPI2 initialization; retained
startup code preserves or inherits that state. Numeric timing therefore stops
at reset-time `fREF` and BOOTMOD/FB_AD or serial-bootstrap configuration before
packaged updater entry `0x80000492`. This is a terminal boundary for the
declared static inputs, not proof that the clock is unknowable. A numeric rate,
finite set, 24 MS/s adequacy, physical behavior, and safe attachment remain
unproved.

For the controlled Track-1 exports, persisted identities 0/1 are independently
correlated with Oneshot/Werp and have an exact conditional route through packet
`+0x094..+0x095` to selector roles 0/1. This is not an observed runtime
publication: selection of that offline record as the live Sound object remains
unproved.

Separately, the static 1.10A-to-1.15 feature boundary joins the introduced
Slice schema/menu entry to public identity `6`, and the exact selector table
maps identity `6` to role 5 in both 1.15 and accepted 1.15C. This is version-
scoped structural provenance, not an observed publication or complete machine.

Logical SRC slot `0x1f` has an already accepted conditional same-coordinate
route from Sound `+0x52` through packet `+0x0e6+n*0x60` to the first SHARC
reader. The manual/descriptor concordance shows that this slot means Start,
WERP Segment Size, or Slice Select by machine. Shared coordinates do not imply
shared algorithms, and selected-live order remains open.

CPU-049 closes the persistence and controlled-value pieces for WERP SEG only:
current-record `+0x6a..+0x6b` changes `0x0100 -> 0x0200` while WERP identity
remains `1`, and authentic decoder/serializer tables join it to `Sound+0x52`.
The route beyond that Sound field remains conditional because no recurring
publisher occurrence is proved to select the exported object. The common first
reader does not establish a SEG-specific downstream consequence or algorithm.

CPU-051 bounds that missing occurrence precisely. The successful import path
consults manager-owned project content and a separately obtained runtime
ordinal before selected-project staging; static MAIN does not join either to
the offline `p=0` export or order the competing refresh/copy/override state at
one named publication. The export is therefore neither proved nor excluded at
runtime, and further static pointer continuation is not justified.

CPU-052 adds a distinct same-WERP MODE axis. Record `+0x6c..+0x6d` changes
`0x0300 -> 0x0000` and maps bidirectionally to `Sound+0x54`, WERP slot `0x20`,
with identity, SEG, all other Sounds, and headers equal. Conditional on live
selection, exact ColdFire flow reaches work `+0x62` and packet
`+0x0e8+n*0x60`. CPU-053 proves that the later generic adjustment can select
the same work word: no match preserves, a match writes through one of two
authenticated helper forms, and the last match wins. Final value preservation
remains open. CPU-054 roots all 24 descriptors and six coefficients for one
recurring occurrence in the mutable publication mirror, then stops because its
invocation-time bytes and last-writer order are unavailable before the first
match predicate. CPU-055 found no qualifying atomic ColdFire capture in the
retained source families. Grid LEN's slot equality is structural, not semantic.

DSP-057 tests the two public-anchored slot encodings only as simulator controls.
They change common lane/table/frame state, and matched role 1/role 4 remains
equal through its bounded two-block screen. This adds a mechanical nonzero
response without closing the missing selected-live publication occurrence.

CPU-050 separately maps seven Grid controls to exact persisted Sound words and
descriptors. Only Grid SLICE currently reuses the accepted slot-`0x1f`
publication coordinate; the other six controls stop at their Sound/descriptor
boundary, and SAMP retains two unresolved auxiliary bytes.

CPU-056 extends only Grid TUNE conditionally: `Sound+0x46 -> compact+0x54 ->
work+0x54 -> packet/snapshot+0xda`. Width, byte preservation, and `raw16/256`
scaling match the accepted DSP slot-`0x19` candidate. Generic adjustment index
`0x19` can overwrite the same work word before packing, however, and its
occurrence content/order is absent. `CROSS-DOMAIN` public-value identity and a
lawful nonzero-low-byte fractional index therefore remain open.

## Project sample-resource receiver boundary

Separate from the recurring state frame, SHARC startup registers event 4 over
a `0x401`-dword receive block. Its `-1` arm parses five dwords into a
1,025-entry sample-resource table; a page arm copies 4 KiB through the shared
address transform. Selection installs one effective base and reaches a signed
16-bit first read and bounded multiply.

CPU-068 closes CPU-066's endpoint to the MCF5441x Rapid-GPIO block, not
FlexBus. MAIN enables `RGPIOBAR=0x8c000035`, drives each byte on RGPIO15..8,
toggles RGPIO7 high-to-low, and polls RGPIO0 ready once per dword. Pin mux,
direction, enable, and slew are exact; numeric rate and board nets are not.

Separately, the SHARC support root closes to LP0 plus DMA30. Source `0x51`
reads LP0_RX and stores the unchanged dword into the indexed transfer buffer
before event 4 and re-arm. Both local endpoints are `STATIC-AUTH`; their
matching direction and 1,025-dword geometry remain `INFERENCE`, not
`CROSS-DOMAIN`. No authenticated external peer/bridge, board wiring,
wire-byte packing, or paired occurrence joins RGPIO pins to LP0, and no
public-channel, source-numeric, or hardware claim follows.

On the CPU side, the publication mutex covers the control word plus the fixed
1,024-dword transaction and keeps the caller's scratch or descriptor block
live until return. It is not a Project backing-group lifetime pin. A
higher-priority endpoint-0 command can remove the final group membership and
make or merge its range reusable before its later zero invalidation attempts
that mutex. The whole-reset path is a negative control because all 1,024
invalidations return before group-vector destruction. This CPU-local permitted
overlap is not a paired occurrence, external acknowledgment, receiver effect,
or coherent/unsafe playback result.

The same SHARC receive descriptor is re-armed only after the selected parser
returns, protecting input-buffer reuse. Destination publication is in place:
the five resource fields are written and read in different sequential orders,
the 4 KiB page has no pointer swap or generation commit, and the installed base
persists separately. DSP-065 authenticates core-0 SEC reset and source-`0x51`
target/enable setup, but the official definition supplies no post-reset
`SEC0_SCTL81.PRIO` value. The retained literal-writer screen finds no direct
PRIO use, and the dispatcher target is unrepresented, so all six
publication/reader windows remain unknown. This proves neither exclusion,
permitted interleaving, wholly-old/wholly-new visibility, nor an occurring
race. Coherence and matching occurrence remain later gates; local completion
is not an external acknowledgment authorizing CPU backing reuse.

Authenticated top-face photographs identify U1 as an MCF54415 and U9 as an
ADSP-21569. All declared Rapid-GPIO and LP0 signal balls terminate first
beneath those BGA packages; visible fanouts stop at vias or hidden layers, and
none of the inventoried test-point/rail/ground observations has a documented
transport identity. This is a photo-scoped `HARDWARE + NEGATIVE-BOUNDED` stop:
it does not prove that the nets are absent. No exact or finite exposed candidate
set survives, so the supplied photographs alone do not justify probe purchase
or attachment for this join.

## Known stop

Transfer timing, physical signal behavior, and external content/order beyond
the accepted M8 gate are not closed. The downstream peripheral boundary is
tracked separately in `IO-01`.
