# OS 1.16: container census, boot chain, co-processors, and the control surface

## Scope and evidence boundary

This dossier concerns **Digitakt II OS 1.16**, one version later than the
1.15C target of the rest of this repository. Everything below is `STATIC-AUTH`
unless labelled otherwise: it rests on a locally obtained update, an
independently written decoder, and static analysis checked against authentic
bytes.

Nothing here was executed on hardware. No image, decoded binary, disassembly,
string dump or authentication material is published.

### Tools referenced

- **[elektron-firmware-tool](https://github.com/mischa85/elektron-firmware-tool)**
  (MIT, by [@mischa85](https://github.com/mischa85)) — an independent C
  implementation of the SysEx transport, the ELE3 container and the LZ77 +
  Elias-gamma codec, in both directions. It is used below for the re-encoding
  result, and it served as an independent cross-check of the container census:
  same section identities and sizes, and decompressed sections byte-identical
  to a separately written decoder. Its `PROVENANCE.md` records it as a
  clean-room implementation containing no vendor code.
- A separately written decoder and decompressor, developed independently for
  this work before that tool was consulted, used for every other claim here.

| | |
|---|---|
| Firmware | Digitakt II OS 1.16 |
| Distribution zip SHA-256 | `dab77ff3a22490a638e309ddb3f0c1ba2f53826f0682c4d8b69e6167f0241958` |
| SysEx transport | 1,880,864 bytes |
| Decoded ELE3 container | 1,484,064 bytes |

## Container census, and the 1.15C delta

1.16 carries **six** sections where the published 1.15C census records five.
Sizes are decompressed unless marked raw.

| Section | Role | Size | Runtime destination |
|---|---|---|---|
| 5 | build-timestamp string | 15 B (raw) | — |
| 2 | bootstrap | 30,302 B | see below |
| 3 | ColdFire MAIN | 3,275,616 B | `0x40000400` |
| 4 | ColdFire updater | 32,776 B (raw) | `0x80000400` |
| 7 | SHARC loader/application | 321,016 B | — |
| 8 | Cortex-M co-processor: USB, USB-PD, MIDI | 159,948 B | `0x60040000` |

Comparing with the 1.15C table already published here: MAIN grows from
3,177,312 to 3,275,616 bytes, the updater stays at 32,776, and SHARC
loader/application content moves from 320,780 to 321,016. Sections 2, 5 and 8
are named here because the 1.16 section table enumerates them; whether the
1.15C container also carries them under a different census is not asserted.

The section table itself is a count followed by 16-byte
`(id, offset, size, load_address)` records. Its offsets are **container-relative**,
i.e. eight bytes less than the corresponding offset in the decoded file, which
is the same eight-byte transport preamble that carries the declared length and
the content checksum. Section 5's record points at the build-timestamp string,
which is how the record layout was confirmed rather than assumed.

## Section 2 is a distinct bootstrap domain

`STATIC-AUTH.` Section 2 is ColdFire code linked at a base of `0x800003FC`,
established by histogramming `pointer - string_offset` pairs: that base scores
168 string-start matches and every competing candidate scores approximately
zero. The section-table load address for this section is a staging coordinate,
not this link base.

This domain is not the updater (section 4). It contains the **STARTUP MENU**
reachable by holding `[FUNC]` at power-on, whose entries correspond one-to-one
with the officially documented recovery path, including the `[TRIG 4]` OS
UPGRADE mode named in the release notes. It also contains the bootstrap
self-upgrade path and the key/encoder/LED test screens.

It additionally owns the **diagnostic boot screen**: a text-only screen
reporting UI, DRAM and USB status, a UI firmware revision, the OS version and
the serial number. The OS version is obtained by reading the firmware header
still resident in nonvolatile storage and trimming its space-padded version
field before formatting — the OS version string is not embedded in this
section.

Architectural observation: boot-time screens are **split across images**.
Anything drawn before MAIN takes over belongs to section 2 or section 4, so a
search confined to MAIN will not find it. This cost real time here and is
recorded so others can skip it.

`NEGATIVE-BOUNDED.` Within section 2 no full-screen bitmap frame was found: a
1 KiB-block image test across the whole section returned no 128x64 candidate.
The diagnostic screen is composed from a 6-pixel proportional font whose width
table is byte-identical to one carried in MAIN.

## Section 8 is a Cortex-M USB and MIDI co-processor

`STATIC-AUTH.` Section 8 carries no ColdFire idioms because it is not ColdFire
code. It is Thumb-2 for a Cortex-M core, linked for execution in place at
`0x60040000`, with a Cortex-M vector table at `0x60042000`: the first longword
is a stack pointer into a RAM region and every following entry is an odd
address inside the image, which is the Thumb bit.

Its reset handler ends in the usual two-table C runtime startup, and the copy
table at `0x60042180` is what makes the rest of the section readable:

| Source | Destination | Length |
|---|---|---|
| `0x600667E8` | `0x20000000` | 2,276 B |
| `0x6005EEB0` | `0x00000000` | 31,032 B |

The second descriptor moves 31,032 bytes to address zero before the application
starts. Several vector-table handlers and the most-called routines in the image
point into that range, so a straight load of the section leaves them dangling
and the section looks like it is missing code. Mapping the block as a second
segment at `0x00000000`, populated from the same bytes, resolves them:
recovered functions rise from 689 to 911 and decoded instructions from 37,322
to 45,730. Source and destination bytes are identical, so the affected routines
appear twice, once at each address.

`STATIC-AUTH.` The image is a FreeRTOS build. Its task table names a timer
service task alongside tasks for USB host, USB device, USB Power Delivery and
MIDI, and its error strings cover flash writes, configuration validation and
firmware-upgrade sequencing. Peripheral usage is consistent: an ADC, two
quad-timer modules, FlexIO, GPIO and four low-power UARTs, identified from
documented register offsets rather than by assuming a memory map.

`INFERENCE.` The reset path configures FlexRAM through an IOMUXC general
purpose register and the part executes in place from `0x60000000`, which is the
i.MX RT family arrangement. The specific part is not established here.

`NEGATIVE-BOUNDED.` Section 8 is not the control-surface processor described
below. Scanning the whole image for Thumb compare-immediate against the panel
link's command bytes returns none, against 2,319 compare-immediate sites in
total. A dispatch built on a jump table rather than on comparisons would evade
this test.

## The control surface is a separate processor behind a serial link

`STATIC-AUTH.` The keys, encoders, LEDs and the display are not memory-mapped
on the ColdFire. They belong to a second processor reached over a ColdFire UART
at `0xEC070000`, in the standard register layout: status at `+0x04` with TxRDY
at bit 2 and RxRDY at bit 0, data at `+0x0C`.

The bootstrap polls that UART directly. MAIN drives it with two eDMA channels
whose transfer control descriptors, at `0xFC045440` and `0xFC045470`, both
address `0xEC07000C`, with a `0x6000`-byte receive ring. The initialiser
requests 156,250 baud from a 132 MHz reference through a divide-by-32
prescaler; the integer divider truncates to 26, so the line runs at roughly
158.7 kbaud.

The link is byte-tagged in both directions. Command bytes, as routing evidence,
recovered from the bootstrap and from MAIN:

| Host to panel | Meaning |
|---|---|
| `B5 slot r g b` | define a palette entry, six-bit components |
| `B9 index colour` | set one LED to a palette slot |
| `B8` | latch the frame |
| `B7 ff` | global LED brightness |
| `1n column b0..b7` | display column write, eight bytes per column |
| `60 mode` | key-scan mode |
| `70` or `71`, `00` | query the panel firmware revision |

| Panel to host | Meaning |
|---|---|
| `2n mask` | key row state, eight keys per row |
| `3n delta` | encoder movement |
| `7n` and four bytes | firmware revision reply |

One bootstrap command, `BA` with a single argument, is not identified.

This explains a structural point that is otherwise puzzling: the display and
the LEDs share one transport, which is why the boot-animation engine obtains
its frame buffer from the same module that owns the LEDs.

### The LED model in MAIN

`STATIC-AUTH.` MAIN addresses 50 LED slots. For each slot it keeps a shadow of
the current palette index, an alternate palette index, a flag selecting between
them, a bit in a dirty bitmap, and a countdown. A tick routine decrements every
countdown and, on expiry, clears the alternate flag and marks the slot dirty.
"Show this colour for N ticks, then revert" is therefore a property of the
driver rather than a timer kept by each caller.

Palette entries 2 through `0x87` are loaded from a table of **134 colours in
three intensity variants**, stride 402 bytes, which is the LED INTENSITY
setting. Entry 1 is the white used by the separate LED BACKLIGHT setting, taken
from a twelve-entry table.

The display is double buffered. One routine pushes all 128 columns; another
pushes only the columns that differ between the two buffers. Both end with the
latch command and swap.

### The power-on LED sweep is not in this container

`NEGATIVE-BOUNDED.` The key-lighting sweep seen at power-on was not found in
any ColdFire image in this container. The search boundary was the complete
call graph of the LED interface: the single-LED setter, the timed-LED setter,
the palette setter, the row flush and the tick routine, together with the 315
call sites of MAIN's high-level set-LED entry point. Those call sites occur
seven to twelve at a time inside per-view handlers. No sweep, no phase table
and no delay-driven loop over LED indices appears among them. In the bootstrap
the entire LED traffic is four palette definitions and five fixed slots lit for
the STARTUP MENU.

`STATIC-AUTH.` The panel runs its own versioned firmware: the bootstrap reads a
revision over the link, prints it on the diagnostic boot screen, and gates on a
minimum minor revision. The 1.16 section table enumerates ids 5, 2, 3, 4, 7 and
8, none of which is a panel image, and neither the bootstrap nor MAIN transfers
code over the link.

`INFERENCE.` The sweep is therefore produced by the panel processor, from
firmware distributed by some other route. Consistent with that, MAIN's first
LED traffic once the link is up is an announce byte, a key-scan mode command
and global brightness set to full, which is the shape of taking over a surface
that is already lit rather than starting one from dark. This is not proved. A
capture of the link at power-on would settle it, and none was taken.

## ColdFire MAIN: reset to first task

`STATIC-AUTH.` The updater hands control to MAIN by **dereferencing** the load
address rather than jumping to it: the longword at `0x40000400` is the entry
point. The chain from there is:

| Stage | Address | Behaviour |
|---|---|---|
| Reset entry | `0x400004E8` | saves the updater's argument, sets SP, configures MCF54415 peripherals, sets `VBR`, enables cache via `CACR`/`ACR0` |
| C runtime | `0x40196CFE` | clears BSS, runs an init call, installs default vectors, enables interrupts, executes `trap #0`, then spins |
| TRAP #0 handler | `0x40000410` | saves registers into the current task control block, selects the next task, `rte` |
| First task | `0x400CC780` | creates a second task and then idles permanently |
| Application main | `0x400CC864` | walks a static-constructor array, runs subsystem init, then branches on one bit of the updater-supplied argument |

The architectural point is that **MAIN is a multitasking RTOS entered through a
software trap**, with the user-facing application running as an ordinary task
rather than from a `main()` loop. This complements, and does not overlap, the
existing account of reset-time *updater* selection, which remains opaque here
too: this dossier begins after MAIN already has control.

`INFERENCE.` The bit tested against the updater-supplied argument at
`0x400CC864` plausibly distinguishes boot modes, but no controlled pair
establishes its meaning.

## The boot animation selector: five variants, but a deterministic choice

`STATIC-AUTH.` The startup animation is chosen at runtime. A selector reads a
15-bit linear congruential generator and walks a cascade of four comparisons,
choosing one of **five** draw routines held in a table of
`(parameter, function, threshold, flags)` records.

The stock thresholds give these **nominal weights**:

| Variant | Nominal weight |
|---|---|
| common | 99.902% |
| rare A | 0.049% |
| rare B | 0.024% |
| rare C | rarer |
| rare D | rarest |

**These are weights, not observed frequencies.** `STATIC-AUTH`: the generator's
state word is referenced from exactly one site in the whole image, inside the
generator itself. There is no seeding function and no other writer, so the draw
sequence is identical on every boot and the selector resolves to the **same
variant every time**. The weights describe what the cascade would do given a
uniform draw; the draw is not uniform across boots, it is fixed.

`HARDWARE, owner-reported.` An instrument running a build with these thresholds
re-weighted toward uniformity showed a single variant across 4-5 restarts, and
a different one from stock. That is the predicted consequence of re-weighting a
deterministic draw: the bucket boundaries move, so the same fixed value lands
elsewhere. Under a genuinely uniform draw, five identical boots would be roughly
a 0.2% event.

Consequently the four low-weight variants are best described as **unreachable
in practice on a given unit**, rather than as rare events. Reweighting alone
does not make them appear; changing which variant is selected does.

`HARDWARE, owner-reported.` A third build confirms this by changing the
mechanism rather than the weights. Replacing the generator's constant additive
term with a read of a free-running 32-bit hardware counter, in the same six
bytes, makes the intro vary between restarts. Three builds each discriminate:

| Build | Predicted | Observed |
|---|---|---|
| stock | one fixed variant | matches |
| thresholds re-weighted | one fixed variant, different from stock | matches |
| thresholds + live counter mixed into the generator | varies per boot | matches |

Had the generator been seeded all along, the first two would have varied; had
the counter been inactive, the third would not have. This is therefore a
confirmed diagnosis rather than a consistent one, and it is the evidence for
the unseeded-generator claim above.

The selected variant draws a 32-pixel-wide logo bitmap at a fixed position on
the 128x64 display. Its constructor arguments and its rendered extent disagree
about the bitmap's height, so the exact dimensions are not asserted here. The
rarest variant is a distinct dither path with its own source artwork and its
own translation unit, identified by a retained source-path string. The four
rare variants are, on the evidence, **boot easter eggs**.

This matters methodologically: artwork reachable only through the rare branches
will look unfamiliar to an owner and can be mistaken for dead content, or for
belonging to an unrelated feature, unless the selector is read first.

`NEGATIVE-BOUNDED.` Within MAIN only three genuine 8-bit grayscale images
exist, verified by a correlation test in which vertical correlation peaks
sharply at the declared width while text and code score near zero over the same
binary.

## Panic screens are pre-rendered, not composed

`STATIC-AUTH.` The crash/panic screen text exists **only as pixels**. The
strings shown on it are absent from the image as text. A panic path cannot
depend on the font and text-layout subsystem still being intact, so
pre-rendering the whole message is the expected design, and its absence from
the string inventory is a positive signal rather than a gap.

`INFERENCE.` A separate exception reporter formats a product-code-bearing
message with a program-counter field, consistent with the same path.

Caution for anyone repeating this: the global bitmap registry is built by one
very long straight-line constructor run with no intervening returns, so
**adjacency of two bitmaps in that run carries no feature relationship**. Two
assets being neighbours there was briefly mistaken for evidence of shared
purpose during this work. The distinctions that do survive are structural —
whether a bitmap is constructed into an array or into a named global — and what
subsequently references it.

## Bitmap assets use two different bit layouts

`STATIC-AUTH.` Assets are not uniformly encoded. Full-screen frames resolve
only as column-major with reversed page order; other assets, including the
logo drawn by the common intro variant, resolve only as row-major, MSB-first.

An incorrect choice is not obviously wrong: it leaves 8-pixel-tall text
legible while corrupting anything taller, which is exactly the failure mode
that makes a wrong layout look plausible. Render both before concluding that an
asset is garbage.

## Container re-encoding and package construction

This section reports on two gates listed as `unproved` in
[modification-gates](modification-gates.md).

`STATIC-AUTH, offline only.` Using the independent MIT-licensed
[elektron-firmware-tool](https://github.com/mischa85/elektron-firmware-tool)
introduced above, a modified MAIN section was re-compressed and a complete container rebuilt,
with the transport preamble's content checksum and the ELE3 cryptographic
trailer recomputed. The rebuilt package self-verifies, and decompressing its
MAIN section returns the modified input byte-for-byte while the remaining
sections are unchanged.

That tool also independently reproduces the container census above: same
section identities, same sizes, and decompressed sections byte-identical to a
separately written decoder. It repacks an unmodified file byte-for-byte.

What this does and does not establish:

- It **does** establish that modified-section re-encoding and complete
  container construction are achievable offline, and that the integrity
  relations are recomputable rather than opaque.
- It does **not** establish installability, device acceptance, minimum-version
  or rollback policy, recovery, timing, audibility or safety. No hardware
  transaction was attempted. The gate dispositions for hardware execution,
  recovery and deployment are unchanged by this dossier.

No authentication secret, derived key or accepted modified update is published
here, and none is required to read this document.

## Earliest remaining opacity

- Whether the 1.15C container also enumerates sections 2, 5 and 8, which would
  make the census difference a reporting artefact rather than a version change.
- The meaning of the boot-mode bit tested at `0x400CC864`.
- Whether the generator is advanced a fixed number of times before the selector
  on every boot, or merely appears so. From a cleared BSS its first draw is `0`,
  which selects the common variant, so the observed non-common selection implies
  prior calls; there are eight call sites.
- Where the DMA timer whose 32-bit counter MAIN reads at `0xFC07000C` is
  enabled. The hardware result above shows it *is* running and free-running,
  since mixing it into the generator visibly randomises the boot intro. But no
  section writes its control registers, so it is configured either outside these
  three images or through an address this analysis could not resolve
  statically. Whether it runs is answered; where it is started is not.
- The meaning of the bootstrap's `BA` panel command.
- By what route panel firmware is distributed, since no section here carries it.
- Everything about the panel link below the byte layer: no electrical or
  timing capture was taken, so the power-on ownership of the LEDs is inferred
  from the firmware's own first traffic rather than observed.
- The bitmap constructor's exact signature. Its pushed dimension arguments do
  not agree with the rendered extent of at least one asset, so either the
  argument order or an implicit padding rule is still misread.
- What the four rare intro variants render, beyond which bitmap objects they
  reference.
- Everything downstream of offline package construction: acceptance, recovery
  and hardware behaviour remain untested and unclaimed.
