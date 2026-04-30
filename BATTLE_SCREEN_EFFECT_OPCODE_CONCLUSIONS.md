# BATTLE Screen Effect Opcode Conclusions

This note captures the evidence-backed conclusions reflected by the current
staged BATTLE screen-effect comment updates in this checkout.

This pass is documentation-only in `_refs/rood-reverse`: it adds clarifying
comments, but does not rename symbols or change behavior.

## Comment-Level Conclusions

- `0xE3` is the shared screen-effect angle tween.
- `0xE4` is the shared two-component screen-effect scale tween.
- `0xE6` is the shared signed two-axis screen-effect offset tween.

## Code Alignment

- [`src/BATTLE/BATTLE.PRG/4A0A8.c`](src/BATTLE/BATTLE.PRG/4A0A8.c) now says the
  shared `0xE2`/`0xE3` angle helper splits cleanly between camera roll and the
  screen-effect angle destination, and that fresh MAP script samples are strong
  enough to confirm `0xE3` as the screen-effect angle tween.
- [`src/BATTLE/BATTLE.PRG/146C.c`](src/BATTLE/BATTLE.PRG/146C.c) now labels the
  `func_8007DDAC` target as the persistent screen-effect angle scalar, alongside
  the already documented neutral shared screen-effect state used by the scale
  and offset paths.
- The same file now documents `func_8007DDB8` as the shared script/menu setter
  for the two scale components.
- The same file now documents `func_8007DDF8` as the shared script/menu setter
  for the signed offset vector.

## Evidence Summary

- The matched dispatch path in
  [`src/BATTLE/INITBTL.PRG/12AC.c`](src/BATTLE/INITBTL.PRG/12AC.c) places `E3`,
  `E4`, and `E6` inside the shared screen-effect helper family.
- The matched angle helper in
  [`src/BATTLE/BATTLE.PRG/4A0A8.c`](src/BATTLE/BATTLE.PRG/4A0A8.c) separates
  `E2` and `E3` by destination and applies the `E3` result through
  `func_8007DDAC`.
- [`src/BATTLE/BATTLE.PRG/146C.c`](src/BATTLE/BATTLE.PRG/146C.c) shows
  `func_8007DDAC` writing one persistent shared scalar, just as
  `func_8007DDB8` and `func_8007DDF8` write the persistent scale and offset
  state.
- [`src/BATTLE/BATTLE.PRG/146C.c`](src/BATTLE/BATTLE.PRG/146C.c) provides the
  concrete shared state touched by those setter paths: a persistent angle
  scalar for `E3`, neutral `0x1000`, `0x1000` scale values for `E4`, and a
  signed offset vector for `E6`.
- Fresh local script decoding adds the script-side confirmation for `E3`: the
  current MAP corpus only uses it inside effect setup clusters, and `MAP102`
  stages angle `48` before tweening back to `0` over 50 ticks.

## Scope Guard

This note is intentionally narrower than a full opcode writeup. It only records
the conclusions that the local code comments now assert with evidence in hand.
The rest of the E-range helper family should stay conservative until its setup
or consumer paths are equally clear.
