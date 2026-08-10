# Host-software boundary

## Accepted host evidence

Hash-bound Overbridge 2.25.7 contains a reachable Digitakt II-specific 80-byte
inbound decoder. Overbridge 2.21.3 supplies an accepted host shared-memory
producer/consumer path and 12 strict read-only frame observations.

The selected DriverKit handler and the shared writer each stop at an indirect
ownership boundary. The observations are `HOSTSOFT-STATIC`/`HOSTSOFT-CTRL`
within their host packages, not physical-device `HARDWARE` evidence.

## Explicit non-claims

The accepted host packages do not establish:

- the exact device-facing DriverKit handler-to-writer call/data edge;
- a deterministic device-frame fixture;
- byte-for-byte correlation with a firmware publication field;
- physical USB behavior, timing, device execution, or hardware safety.

Host and firmware evidence remain separate until an independent exact join is
proved.
