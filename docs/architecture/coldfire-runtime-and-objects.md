# ColdFire runtime and object architecture

## MAIN ownership

ColdFire MAIN owns user-facing source identities, persistence/migration,
project and Sound objects, parameter defaults, sequencer/edit state, task and
callback machinery, packet construction, and recurring publication.

The thirteen displayed machine entries are presentation-level resources rather
than a one-to-one SHARC dispatch table. Accepted numeric source identities are
`0..6`; presentation order, legacy encodings, and runtime selection are
separate layers.

A controlled project-export pair gives one bounded public anchor. Track 1
Oneshot-to-Werp changes persisted current-record `+0xd2` from `0` to `1`, and
the authentic decoder stores that field at Sound `+0xa2`. The other three
changed record bytes remain unknown initialized companion/default candidates.
The offline record is not proved to be the selected live object at a recurring
publication occurrence.

The official OS 1.15 manual and authentic MAIN registry support a bounded
parameter-schema concordance for Oneshot, Werp, Stretch, Repitch, Grid, and
Slice: 40 manual-visible parameters join to descriptors, while 15 assigned
positions are manual-absent and five are unassigned. Logical slots are reused
with machine-specific meanings, so slot equality is structural and does not
prove shared normalization or algorithm behavior. Individual parameter
persistence and selected-live ordering remain open.

One controlled exception now closes WERP SEG persistence. In a hash-bound pair
where WERP identity remains `1`, the only changed current-Sound byte is record
`+0x6a`, making word `+0x6a..+0x6b` change `0x0100 -> 0x0200`. Authentic
current-v4 decoder and serializer tables map that word bidirectionally to
`Sound+0x52`, and the authentic WERP SEG descriptor assigns slot `0x1f`.
Every non-target Sound and project header is equal. A target-specific static
occurrence audit then stops at manager-owned project content/order: the saved
object is neither proved nor excluded at one recurring publication, and SEG
mechanics remain open.

An adjacent same-WERP MODE edit independently isolates record
`+0x6c..+0x6d` (`0x0300 -> 0x0000`). Authentic inverse tables and the WERP
descriptor bind it to `Sound+0x54`, logical slot `0x20`; identity, SEG, all
other Sounds, and project headers remain equal. This is a persisted public-
control anchor only. The offline Sound is not proved selected, and a later
generic indexed adjustment can authentically select the corresponding work
word. Its exact decision contract is no match preserves, a match writes, and
the last match wins. One named recurring occurrence now closes all 24
descriptor and six coefficient roots through the publication mirror. Its
invocation-time mirror bytes and last-writer order remain absent before the
first index test. A bounded retained-source inventory found no qualifying
atomic ColdFire capture, so the recurring value remains indeterminate.

A controlled Grid staircase adds seven exact persisted-field anchors: TUNE at
`Sound+0x46`, PLAY at `+0x48`, SAMP at `+0x4c`, SLICE at `+0x52`, LEN at
`+0x54`, GRID at `+0x56`, and LEV at `+0x58`, each joined to its authentic Grid
descriptor. All non-target Sounds and headers remain equal. SAMP also changes
two auxiliary logical bytes whose current owner and meaning remain open.

The official 1.10A-to-1.15 differential provides a separate version boundary.
All 265 old descriptor records align one-to-one; ten are inserted: one
coincident Oneshot Crossfade row and the nine-row Slice schema. Of the aligned
records, 201 raw descriptor domains renumber by `+1`, including three old
filter rows from `6` to `7`. The raw domain is therefore not public identity.
Independent source-menu, editor, persistence, and selector evidence instead
binds the new Slice schema to public identity `6`.

## Persistence and object model

Accepted slices cover current and legacy Sound serialization, migrations,
manager selection, multiple object aliases, callback-vector mutation, queue
ownership, and exact helper/setter paths. These prove specific object and field
relationships, not the entire class hierarchy or scheduler graph.

Parameter names, table adjacency, defaults, and cardinality remain orientation
unless authentic read/write/dataflow proves a semantic assignment.

## Sampling save and active-track assignment

Before enqueue, the authenticated save scheduler copies one chosen path
byte-preservingly into sibling work and completion closures. The fixed work
callback passes its copy through the recorder/WAV writer, final header update,
finalization, and close. Only after work return is the retained completion
posted as a type-6 main-loop event; its fixed target and sole factory construct
`AssignPrompt`, copying the same path bytes into object `+0x58` and storing an
independently allocated first-free Project slot at `+0x5c`.

The prompt's later track-key handler captures those two fields, resolves a
16-byte descriptor, admits or reconstructs it through Project RAM, and writes
sample parameter `0xcd` to receiver state `+0x4c`. Authentic vtables bound the
complete active-track writer family to exactly two receiver targets. The
previous report's `SampleListView+0x58/+0x5c` owner and closed-file versus
independent-selection nonjoin are superseded; that report remains preserved.

This is an exact static path-identity and callback-order bridge, not a PCM
identity bridge. No PCM pointer, backing owner, file handle, or pre-save
descriptor crosses the boundary. Runtime scheduling, filesystem success,
actual path/slot content, user track-key occurrence, Project-RAM backing, DSP
publication, hardware behavior, and feature feasibility remain open.

## Active-pattern transition layer

The accepted four live-pointer writers form one finite eight-edge mechanical
graph. A 24-byte record enters the queue; its state-zero branch can install
record field zero synchronously. The registered update routine separately
promotes a transition target or one pending pointer under three exact predicate
families, preserving live-to-previous ordering before promotion.

PatternSelect and PatternChain each construct the same bounded record and call
the exact queue input. PatternChain has no direct caller in the retained screen;
PatternSelect has direct call reachability but no selected occurrence. Target-
arm eligibility and target content have no producer in the bounded formed and
10,522-function resolved-constant screen, while alternate provider
target/content/order remains indirect. This is structural `STATIC-AUTH` plus
`NEGATIVE-BOUNDED`, not public switching, chaining, playback, timing, trig, or
parameter-lock semantics.

The complete bounded exact-literal/resolved reader universe contains 38 reads
across twelve neutral families. Initialization registers a source-44 level-5
handler and a source-57 level-2 successor; the accepted recurring publisher
only conditionally forces source 44. That handler conditionally reads LIVE,
then signed byte `LIVE+0x1db79` and compares it with one. External byte content,
owner, runtime gate, occurrence/cadence, selected successor, unresolved aliases,
and every public event meaning remain open. This is a structural consumer
boundary, not observed sequencer execution.

The ordered source-57 handler has one complete ten-phase static map: 23 LIVE
reads, three writes, 45 calls, 147 conditional branches, and 21 exact
sixteen-iteration loops. Its unconditional prefix calls active provider slot
`+0x4`; target, returned content, side effects/order, and occurrence are the
first opacity. A later loop reads `TARGET+i*0x6a5+0x48f`, applies a table and
subtract-one transform, and writes byte state. Because it follows the provider
stop and later gates, it is structural capability rather than selected behavior.

## M8 provenance boundary

The bounded M8 campaign recovered:

- source-array and record-to-state coordinates;
- factor-A serialization and startup/default state;
- current-pattern and active-manager selection;
- callback/vector mutation mechanics;
- parser metadata windows, queue direction/order, and event-ingress families;
- exact joins into packet/work coordinates used by later SHARC synthesis.

The lane now ends at external content and scheduling opacity. More pointers,
aliases, callbacks, or xrefs do not justify continuation. Reopen it only when a
separately selected DSP operation requires one exact missing input or order
edge.

CPU-030 and CPU-034 are retired coordination records. They produced no
technical evidence and were replaced by bounded completed tasks; interruption
was not promoted into a negative result.
