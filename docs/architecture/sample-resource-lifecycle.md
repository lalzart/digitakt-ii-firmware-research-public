# Project sample-resource lifecycle

This dossier follows one question across both processors:

> How do recorded bytes become Project-owned sample backing, and how far can
> that identity be followed into SHARC playback?

The answer is now deeper than a simple “save, assign, play” diagram. The file
format, Project indirection, CPU publication endpoint and SHARC receiver are
individually closed. Three joins remain open: recorder-native ownership to
Project backing, the physical CPU-to-SHARC bridge, and active-playback retention
of the selected backing.

## End-to-end map

```text
external recorder service
  -> transient response aperture
  -> MAIN save staging
  -> 64-byte header + payload + 16-byte trailer
  -> finalized sample file
  -> provider read / optional stereo lane split
  -> Project backing group and signed slot S
  -> five-word resource descriptor + 4 KiB pages
  -> ColdFire Rapid GPIO byte/strobe/ready endpoint
       -/-> external peer, wiring and dword packing remain unknown
  -> SHARC LP0_RX / DMA30 / source 0x51
  -> event-4 resource record or page update
  -> installed effective base
  -> first signed 16-bit sample-word read
```

The `-/->` edge is deliberate: both local endpoints are authenticated, but they
have not been joined into one captured physical transaction.

## 1. Recorder service and save boundary

The selected recording command requests a source selector, start and count
from a service outside the recovered MAIN ownership boundary. Successful mono
content is exposed as two bytes per frame and stereo as four bytes per frame in
a transient shared response aperture. MAIN copies the valid response into
separate save staging before releasing the service mutex.

This closes the selected response-to-file byte route. It does not identify the
native recorder buffer owner, allocation lifetime, invalidation rule, numeric
signedness, normalization, byte significance or public left/right identity.

## 2. Saved sample format

The accepted writer at `0x401463a8` constructs:

```text
0x40-byte header
N-byte payload copied unchanged from save staging
0x10-byte trailer
```

The recovered header fields used by the Project path include:

| Header field | Accepted role |
|---|---|
| `+0x01` | `0` mono, `1` stereo |
| `+0x04` | big-endian payload byte count `N` |
| `+0x08` | sample rate `48000` |

The provider writer/reader pair at `0x4014ada2` and `0x4014a986` preserves
payload bytes by offset. Mono stays in one ordered lane. For stereo, helper
`0x40145fa0` sends each frame’s first 16-bit word to lane A and second word to
lane B, preserving the two bytes within each word and frame order within each
lane.

This is an exact representation result, not yet an audio interpretation. The
evidence does not assign signed PCM, endian significance or public L/R to the
two words.

## 3. Project slot and backing indirection

Save completion and assignment meet at an `AssignPrompt`: the finalized path
is retained, while the destination Project slot is obtained independently. A
later handler resolves a 16-byte, four-word provider identity and enters
Project-RAM admission.

Admission has three bounded outcomes:

- retain unchanged backing;
- reuse a matching provider group; or
- read/map new backing and install the provider identity after success.

The track does not retain a raw sample pointer. Parameter `0xcd` writes only a
signed 16-bit Project slot into track receiver state `+0x4c`. A later chain
resolves that slot through global Project state to provider identity, extent,
backing-record base and flags.

This distinction matters: **slot identity is not backing ownership**.

## 4. CPU publication endpoint

Project backing is represented to the downstream service as a five-word slot
descriptor plus fixed 4 KiB page transactions. The recovered ColdFire endpoint
uses the Rapid-GPIO block at `0x8c000000`:

- `RGPIOBAR=0x8c000035` enables the block;
- RGPIO15..8 carry one byte at a time;
- RGPIO7 is strobed high-to-low;
- RGPIO0 is polled as ready once per dword.

This excludes the previously considered FlexBus route and closes the
processor-side byte/strobe/ready protocol. It does not establish the external
peer, board nets, transfer rate, voltage, dword packing or one paired device
occurrence.

## 5. SHARC receiver and first read

On the SHARC side, source `0x51` reads LP0_RX and stores the unchanged dword
through DMA30 into the indexed transfer buffer. Startup registers event 4 over
a `0x401`-dword receive block.

The event handler has two relevant forms:

- a resource-record arm parses five dwords into the 1,025-entry sample-resource
  table;
- a page arm copies one 4 KiB page through the accepted address transform.

Selection installs one translated effective base. The accepted downstream path
then reaches a signed 16-bit first read and a bounded multiply. This is the
first authenticated sample-word consumer in the selected route, not a complete
playback algorithm or public-channel assignment.

## 6. Update/read synchronization

The SHARC parser finishes before its input receive descriptor is re-armed, so
the input buffer is not locally reused during parsing. Destination publication
is less settled:

- resource fields are written and read sequentially in place;
- 4 KiB pages are copied in place;
- no generation counter, pointer swap or atomic commit was found;
- the installed base persists separately from the record/page update.

Core-0 SEC reset and source-`0x51` enable/target setup are authenticated, but
post-reset priority, the dispatcher target and incoming interrupt mask are not
represented in the retained inputs. The six mapped writer/reader windows
therefore remain unknown. Neither exclusion nor an occurring race is proved.

## 7. Backing lifetime and active playback

CPU-side scratch pages and descriptor blocks survive until their synchronous
mapping call returns. The global Project backing group is not pinned by that
transaction mutex. A higher-priority valid removal can erase the final group
membership and make its range reusable before the later invalidation call
reaches the same mutex.

That is a permitted CPU-local interleaving, not an observed corruption or
playback result.

A separate search followed an authentic type-`0x0c` start route into two
per-track scheduler-state builders and closed their local refcount mechanics.
The refcount is real but scheduler-local. The start route’s track ordinal and
Project-record pointers do not join the distinct track receiver carrying slot
S at `+0x4c`. The investigation therefore stops before it can prove either:

- a playback-scoped pin or snapshot of Project backing; or
- the absence of such an owner elsewhere at runtime.

## Physical evidence boundary

Authenticated top-face photographs identify the ColdFire package as an
MCF54415 and the SHARC as an ADSP-21569. All declared Rapid-GPIO and LP0 signal
balls disappear beneath BGA packages. Visible fanout terminates at vias or
hidden layers, and none of the inventoried test-point/rail/ground observations
has a documented transport identity.

This supports a bounded “no attributable probe set from these photographs”
result. It does not prove that the connection is absent or that no board-level
measurement is possible with additional documentation or access.

## What this means for a Flex-like direction

The recovered architecture does not reveal a dormant Flex subsystem or an
existing direct recorder-buffer alias. A plausible conservative workflow is:

1. finalize recorder bytes into a temporary Project sample;
2. publish and play that backing without mutation; and
3. release it only after an explicit stop and closed lifetime rule.

Continuous live overwrite or seamless replacement would require deliberately
closing ownership, pinning, synchronization, replacement, receiver visibility
and UI/lifecycle behavior. This is an engineering implication of the recovered
boundaries, not a proved feature design or authorization to modify firmware.

## Evidence routing

| Result family | Accepted checkpoint | Evidence |
|---|---|---|
| Save/assignment and Project indirection | CPU-062..064 | `STATIC-AUTH`, `NEGATIVE-BOUNDED` |
| Recorder/backing comparison and byte format | CPU-065..067 | `STATIC-AUTH`, `NEGATIVE-BOUNDED` |
| CPU Rapid-GPIO endpoint | CPU-068 | `STATIC-AUTH`, `NEGATIVE-BOUNDED` |
| CPU publication lifetime | CPU-069 | `STATIC-AUTH`, `NEGATIVE-BOUNDED` |
| Active-playback retention stop | CPU-070 | `STATIC-AUTH`, `NEGATIVE-BOUNDED` |
| SHARC receiver, owner and scheduling | DSP-062..065 | `STATIC-AUTH`, `NEGATIVE-BOUNDED` |
| Photo-scoped probe feasibility | LAB-005 | `HARDWARE`, `NEGATIVE-BOUNDED`, `INFERENCE` |

No result in this dossier establishes installability, hardware execution,
audibility, timing margin, replacement safety or permission to flash a device.
