# BATTLE Screen Effect Opcode Conclusions

This note captures the evidence-backed conclusions reflected by the current
staged BATTLE screen-effect comment updates in this checkout.

This pass is documentation-only in `_refs/rood-reverse`: it adds clarifying
comments, but does not rename symbols or change behavior.

## Comment-Level Conclusions

- `0xE4` is the shared two-component screen-effect scale tween.
- `0xE6` is the shared signed two-axis screen-effect offset tween.

## Code Alignment

- [`src/BATTLE/BATTLE.PRG/4A0A8.c`](src/BATTLE/BATTLE.PRG/4A0A8.c) now says the
  matched consumer path in `func_800BDAB4` is strong enough to lock in `E4` as
  the scale tween and `E6` as the offset tween, while keeping the remaining
  nearby opcodes conservative.
- [`src/BATTLE/BATTLE.PRG/146C.c`](src/BATTLE/BATTLE.PRG/146C.c) now labels the
  reset state as neutral shared screen-effect state, which matches the scale
  and offset interpretation used by the helper comment.
- The same file now documents `func_8007DDB8` as the shared script/menu setter
  for the two scale components.
- The same file now documents `func_8007DDF8` as the shared script/menu setter
  for the signed offset vector.

## Evidence Summary

- The matched dispatch path in
  [`src/BATTLE/INITBTL.PRG/12AC.c`](src/BATTLE/INITBTL.PRG/12AC.c) places `E4`
  and `E6` inside the shared E-range screen-effect helper family.
- The matched apply side in
  [`src/BATTLE/BATTLE.PRG/4A0A8.c`](src/BATTLE/BATTLE.PRG/4A0A8.c) is enough to
  distinguish the scale setter path from the offset setter path.
- [`src/BATTLE/BATTLE.PRG/146C.c`](src/BATTLE/BATTLE.PRG/146C.c) provides the
  concrete shared state touched by those setter paths: neutral `0x1000`,
  `0x1000` scale values for `E4`, and a signed offset vector for `E6`.

## Scope Guard

This note is intentionally narrower than a full opcode writeup. It only records
the conclusions that the staged comment changes are asserting in code. The rest
of the E-range helper family should stay conservative until its setup or
consumer paths are equally clear.
