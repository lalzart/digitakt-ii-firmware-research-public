# DSP processing and state contracts

## Coefficient path

For one controlled invocation, six signed 16-bit source samples multiply six
signed 32-bit coefficient lanes, accumulate in MRF, reduce arithmetically,
saturate, convert under controlled `r6=0`, and store one 32-bit word. This is
an exact `STATIC-AUTH + SIM-AUTH + SIM-CTRL` equation for that invocation.

Three consecutive authentic rows plus the joined fixed-point position,
source-window, row modifiers, and advance/wrap support a strong bounded
phase-indexed interpolation/resampling classification. The source contract has
ten unique P words. Across 13 lawful retained schedule hashes and 790 matched
calls, the gate is zero and execution returns before the selected tuple/loop.

Full row extent, exact ratio, saturation-active behavior, lawful gate-open
state, public algorithm identity, and hardware meaning remain open.

## Six-vector merge

The authentic reached loop at `0x001c2030..0x001c2070` performs six independent
32-position updates. Each A/B work-vector word receives its prior value plus
one incoming coefficient times XA/XB, while three coefficients advance by
incoming increments. Parallel bundles use pre-bundle sources; no output uses a
just-written work-vector value or another word inside this loop.

An eight-case exact-dyadic `SIM-CTRL` matrix matched all 3,072 predicted writes
and passed sign, scale, superposition, repetition, and sham controls. This
supports only a bounded linear/matrix-style `INFERENCE` for that fixed
coefficient schedule. The direct post-loop frontier carries the six families
through exact C/D/E ranges to return with no F0/F1 access; the first later F0
write consumes distinct Z0. Post-return aliases, lawful state, and public
meaning remain open.

## ROOT stage

The stage at `0x001c232c..0x001c2371` has an exact two-bank, read-before-write,
64-output equation and three-word state update. Across the retained schedules,
it is strongly classified as a block-to-block scalar gain smoother.

The selected target joins through selected-image `m10+0x732`, packet `+0x732`,
compact/transformed source `+0x98c`, recurrence result 611, and prior-expanded
state `+0x1318`. All 19 accepted payload target words, spanning six hashes, are
zero. Lawful nonzero recurrence and public meaning remain opaque.

## B-consuming routines

Five B-consuming callees are structurally joined. For `0x001cdb46`, the q=0
path closes exact 32-word access/write and return but stops numerically at entry
`f14/f15` consumed before local assignment.

Two exact callers enter shared `0x001cbe9b`. Caller B alone has a count/DAG-
dependent pre-body. Selected schedules close frame count 32, register/circular-
DAG state, 32 zero B reads/writes, return, and reconvergence. Four persistent
state accesses differ by signed zero.

The selected reciprocal instruction maps controlled `32.0` to raw 40-bit
`R12=0x3cff800000`; the helper return and first B-word equation are exact.
Returned `r8` is overwritten before arithmetic at `0x001cbf15`. The complete
selected loop preserves every zero B word in place, takes 31 back edges, and
reconverges the signed-zero/raw-flag difference by iteration 1.

Finite positive/negative controlled screens establish only that:

- the B interface is word-local within the matrices;
- tested in-range values pass unchanged;
- `+2.0` and `-2.0` clip to `+1.0` and `-1.0`;
- five matched response vectors are exact sign mirrors;
- isolated P+4 state and one tested nonzero arena coordinate change internal
  position state without a B effect on the observed `SV`-clear path.

A separate byte-matched `PASS`/`SV` microfixture screen closes exactly fifteen
frozen zero-guard raw-40 operands: all survive `PASS`, leave `SV` clear, and
fall through twice. An explicit incoming-SV sham preserves the flag and takes
the branch twice. Because no non-sham candidate takes, the authentic full-loop
branch arm, arena representation, state term, and B consequence remain
unexecuted and open.

These controls do not prove a global limiter law, lawful device state, a public
filter/effect, audibility, hardware behavior, or modification preference.
