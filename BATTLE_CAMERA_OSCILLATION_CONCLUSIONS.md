# BATTLE Camera Oscillation Conclusions

This note captures the evidence-backed conclusions reflected by the current
`0xEF` camera-oscillation comment update in this checkout.

This pass keeps the code change comment-level only in `_refs/rood-reverse`,
but the recovered raw handler behavior is now strong enough to tighten the
opcode-level conclusion.

## Comment-Level Conclusions

- `0xEF` is a camera oscillation control opcode, not a `SCREFF2` parameter
  opcode.
- A zero second data byte clears both local oscillation slots immediately.
- A nonzero second data byte sets up one of two oscillation slots with phase
  rate, amplitude, control, and duration fields.
- The low control byte is already strong enough to treat it as target-routing
  plus packed axis-multiplier bits.

## Code Alignment

- [`src/BATTLE/BATTLE.PRG/4A0A8.c`](src/BATTLE/BATTLE.PRG/4A0A8.c) now says the
  `func_800BDC9C` cluster contains the `0xEF` camera-oscillation setup/clear
  path rather than a `SCREFF2` path.
- The same file now records that `func_800BE180` advances those paired slots
  and turns the low control byte into routed camera-relative offsets for
  `cameraLookAt` and `cameraPos`.

## Evidence Summary

- The `0xEF` arm in the raw `func_800BDC9C` handler clears both slot-duration
  bytes when the second script data byte is zero.
- The non-clear path picks one of two slot records and stores:
  - the first script byte as the phase-rate input
  - the second script byte as the amplitude input
  - the low control byte in the slot header consumed by `func_800BE180`
  - the high control byte in the slot header consumed by `func_800BE01C`
  - the last byte as duration
- The recovered `func_800BE01C` body feeds the first byte into the oscillation
  phase term and multiplies the second byte into the final `rsin` output.
- The recovered `func_800BE180` body extracts target-routing bits and packed
  axis multipliers from the low control byte before applying the results to the
  camera vectors.

## Scope Guard

This still does not claim final user-facing names for each high-byte waveform
mode. The confirmed conclusion here is the opcode-level subsystem and the
control/setup split, not a finished vocabulary for every oscillation variant.
