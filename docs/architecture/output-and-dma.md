# Output-bank and DMA architecture

## Selected-bank transform

The root-copy path selects one of two 64-word banks:

- bank 0: `0x00261cb8..0x00261db4`;
- bank 1: `0x00261db8..0x00261eb4`.

At `0x001c7555..0x001c755e`, authentic code reads every word and overwrites it
in place with sign negation. A corrected offline fixture proves a same-width
three-byte replacement performs pass-through while pristine/sham retain
negation; the difference persists through transform exit, return, and
controlled stop with declared unrelated regressions preserved.

## Descriptor and peripheral ownership

The post-overwrite bank is owned structurally by a two-bank circular descriptor
around `0x002620b8`. L2 code installs the aliased head in
`DMA10_DSCPTR_NXT=0x31023000` and configures SPORT4 half A for transmit with
the accepted control value containing `SPTRAN` bit 25.

The simulator does not emit the asynchronous descriptor payload fetch. Exact
fetch time, ordering against an overwrite, physical pin/connector, cadence,
audible endpoint, utilization, and safety remain unobserved.
