# SHARC runtime and dispatch architecture

## Recurring execution

The accepted operational path covers FreeRTOS startup, Audio Task creation and
notification wait, recurring root `0x001c74cd`, main processor `0x001c2b24`,
per-unit/lane traversal, common processing, output copy, and return. Status 0
returns, status bit 0 performs housekeeping, and status bit 1 enters processing.

This is an accepted retained-path model, not a complete all-state scheduler or
hardware timing model.

## Identity and selector roles

Lawful identities `[0,1,2,3,4,5,6]` map to internal selectors
`[0,1,2,3,4,0,5]`. The six targets are:

| Role | Target | Coverage |
|---|---|---|
| 0 | `0x001c65bd` | `SEL-00` |
| 1 | `0x001c6715` | `SEL-01` |
| 2 | `0x001c6782` | `SEL-02` |
| 3 | `0x001c686e` | `SEL-03` |
| 4 | `0x001c692f` | `SEL-04` |
| 5 | `0x001c6992` | `SEL-05` |

All retained paths reach first common reconvergence `0x001c65fe`. The compact
topology contains 261 blocks, 66 direct calls, 189 exits, and 857 accesses;
role-exclusive, subset-shared, and common work are separated. No role accesses
B before the common boundary.

The controlled export pair maps public Oneshot/Werp to numeric identities 0/1
and therefore conditionally to roles 0/1. It does not identify either role's
algorithm or make the role mapping one-to-one; identity 5 also selects role 0.
The manual-to-descriptor concordance adds public parameter schemas, but no new
instruction edge: slot reuse and documented behavior do not name a selector
role or a complete DSP algorithm.

The 1.10A-to-1.15 differential supplies the missing static version provenance
for Slice: public identity `6` and selector role 5 are added together with a
new role-5 preparation block. Its 135-instruction helper shares 50 exact
instructions with the earlier 97-instruction Grid helper, supporting bounded
shared ancestry rather than a wholly isolated or fully decoded algorithm.

DSP-051 closes that ancestry mechanically. Grid and Slice share neutral
clamp/scale, fixed/integer packing, short-gate, utility-call, and conditional
state-update structure. Slice adds endpoint/state comparisons,
`P+0x1a8/+0x1ac` fixed-state writes, four packed words at
`P+0x198..+0x1a4`, and alternate arms. All eight accepted identity-6 windows
take the first zero-gate restore path; the normalization return, gate-open arms,
individual parameter join, complete algorithm, and audible meaning remain open.

DSP-052 then tests whether role-specific state reaches one common reader. In
all 24 accepted role/block observations, the internal role value produces a
five-plus-one candidate-generator split: roles 0, 1, 3, 4, and 5 call
`0x001c4ecf`, while role 2 calls `0x001c5576`. Both candidates read the same
unresolved `P(q)+0` pointer and zero `P(q)+0x1b8` gate, then zero-fill B before
any authenticated sample-data dereference or six-tap-reader landmark. Thus the
all-six exact-reader claim is rejected for this controlled screen, while the
broader modular shared-machinery model and actual gate-open reader ownership
remain open.

DSP-053 authenticates both candidate gate formulas as the same byte
`P(q)+0x1b8`. The constructor and other direct P-relative reset writer are
zero-only. Across 13 accepted hashes, 416 constructor-zero writes precede
1,200 zero reads with no intervening gate write. DSP-054 closes entry
`0x001c5615` as the direct role-2 positive branch. Exact DAG arithmetic and six
register-preserving calls prove its later stores reset the same byte after the
read; none of 13 schedules takes that arm. DSP-055 then closes the frozen
initialized universe: 55 normal records, 30 P-relevant cross-record calls, nine
direct targets, and 21 initialized table entries contain no nonzero
post-constructor pre-read writer. This is not a universal-zero claim: the first
remaining source boundary is runtime-generated or uninitialized external
owner/content and its write-before-read order.

DSP-056 follows logical slot `0x1f` independently of those generator gates.
The common reader converts and scales its signed low half into per-lane state.
Across 48 accepted zero-valued windows, none of the six selector-role bodies
reads, overwrites, or kills that state; all first consume it after
reconvergence through the same shared frame/callee calculation. Role 1 has no
distinct slot-derived consequence in this screen. Alternate state becomes
opaque at `0x001cc04d`, so this is shared parameter processing rather than a
public WERP/Grid/Slice algorithm identity.

DSP-057 supplies a bounded nonzero discriminator with complete accepted
processor live-ins. Controlled words `0x0100/0x0200` change the common scaled
lane value, indexed-table row, and selected frame state. For matched `0x0100`,
roles 1 and 4 remain exact through four selected common chains and the declared
P/B/scatter/ROOT/output-family screen. The values remain `SIM-CTRL`; this is
not selected-live state, matched public meaning, or an algorithm claim.

DSP-058 corrects the next control boundary. `frame+0x28 -> LEFTZ -> SV` sends
zero first to `0x001cc088` and nonzero first to `0x001cc050`; both paths reach
common `0x001cc065`. An authentic first-gate-selected setter can write `-1`,
but every accepted schedule initializes, reads, and resets that gate as zero.
The nonzero gate producer remains externally opaque, so no fixture is justified.

DSP-059 closes the selected slot-derived return lineage rather than extending
that producer search. `0x001cc192` returns the controlled raw `-0.0/+0.0`
distinction in `F0/R0`; caller `0x001c66a5..0x001c66c4` leaves it untouched.
First read `0x001c66c7` adds accepted `F2=+0.0` and commits equal `F7=+0.0`,
so it is the exact kill/reconvergence in roles 1/4, q0/q1, blocks 1/2, and the
declared repeated controls. Other addends/nonzero returns remain open.

DSP-060 adds two earlier all-role shared parameter primitives using controlled
SHARC candidates. Candidate slot `0x19` is normalized into `lane(q)+0x70`,
read at common `0x001c69d3`, and passed to direct table-index path
`0x001cd8b3..0x001cd8f3`; the bounded control moves its first table access
from `0x0026ec74` to `0x0026ed64`. Candidate slot `0x1a` reaches
`lane(q)+0xa4`, common frame `+0x1c`, and direct scalar path
`0x001cc183..0x001cc192`, computing `x-2*x*x`. All six roles share these exact
lineages, while slot `0x1b` remains a distinct control. CPU-056 conditionally
matches Grid TUNE to slot `0x19` by coordinate, width, and scaling, but a
same-word pre-packet writer leaves public-value survival and `CROSS-DOMAIN`
identity open. PLAY, complete algorithms, and audio meaning remain open.

DSP-061 follows the slot-`0x19` candidate through that table. Loader record 32
uniquely initializes a 128-word binary32 table. The routine reads adjacent
words and writes their fractional blend to q0 state; for both accepted integer
indices the upper word is zero-weighted, the adjacent lower word supplies the
changed value, and the selected register dies before the next helper. Roles 0
and 5 match exactly. The state buffer is not read before its same-value next-
block overwrite, and no lineage reaches accepted phase or sample-reader
landmarks. This is shared interpolation/blending machinery, not public TUNE,
pitch, phase, sample navigation, or a complete engine.

For the repeated controlled identity-1, `q=0`, zero-payload screen, the role-1
path is mechanically closed from `0x001c6715` through first common
`0x001c65fe`. Its selected prefix performs exact scalar clamp/shift, zero-input
scale, `1/32` reciprocal, and frame preparation before a subset-shared P-state
transition. At common, six register/DAG residues plus inherited `S(q)=1`
differ from the role-0 control; the selected P coordinates are equal and B is
untouched. The first universal-path opacity is the unobserved helper
fallthrough at `0x001c06c6`. No post-common consequence or complete WERP
algorithm is accepted. In the bounded shared-path frontier, `R10`, `R11`, and
`R7` are killed before read; `R3`, `R6`, and `I12` survive unchanged only to
direct call `0x001c669e -> 0x001cc171`. Their formal argument status and first
callee use/kill were then bounded further: `I12` is killed before read at
`0x001cc046`, while untouched `R3/R6` stop at selected indirect target
`0x001cc092`. No residue has a proved downstream consequence in these screens.

This is not a universal CFG. Alternate data-dependent successors, unproved
bases, and public meanings remain open. Selector 0 stops at alternate branch
state near `0x001c49b4`; selector 5 stops at the missing lawful nonzero producer
for `P(q)+0x1ba`.

## Shared graph

After reconvergence, the accepted graph joins generator/mode processing,
scatter, six work-vector families, a distinct sibling path, F0/F1, ROOT0/ROOT1,
and output. The six-vector overwrite is an exact 32-position coefficient-
scheduled multiply-add over three A/B pairs and auxiliary XA/XB. Its direct
post-loop frontier reaches exact C/D/E ranges and returns without an F0/F1
join; the first later F0 write consumes distinct Z0. Post-return aliases remain
open.
