# Jump-Back Counter Opcode Conclusions

This pass confirms battle-script opcodes `0x49` and `0x4A` as the local
jump-back counter pair used for counted and unconditional backward loops.

## Conclusion

- Best confirmed names:
  - `0x49 -> SetJumpBackCounter`
  - `0x4A -> JumpBackIfCounter`
- `0x49` writes a counter byte into the local `D_800F4C10[]` slot array when
  the selector is not `0xFF`.
- `0x4A` jumps backward immediately on selector `0xFF`, or pre-decrements a
  selected `D_800F4C10[]` slot and jumps backward only while that decremented
  value remains nonzero.

## Evidence

- The matched dispatch table in `src/BATTLE/INITBTL.PRG/12AC.c` maps `0x49` to
  the readable handler now renamed
  `vs_battle_script_setJumpBackCounter` and maps `0x4A` to
  `vs_battle_script_jumpBackIfCounter`.
- The `0x49` handler body in `src/BATTLE/BATTLE.PRG/4A0A8.c` only stores
  `arg0[2]` into `D_800F4C10[arg0[1]]` when `arg0[1] != 0xFF`, proving that
  the opcode is local counter setup rather than control flow by itself.
- The paired `0x4A` handler in the same file returns
  `&arg0[-vs_battle_getShort(arg0 + 2)]` immediately on selector `0xFF`, which
  proves the unconditional backward-jump form.
- The non-`0xFF` path instead executes `--D_800F4C10[arg0[1]] != 0` before
  returning that same backward target, which proves the counted-loop behavior.
- Local decoded-script usage matches both forms:
  - `decoded_scripts/23-Great Cathedral/131-Escape from Leà Monde.txt`,
    `133-Collapse of Leà Monde.txt`, and
    `413-Sydney reaches Hardin and Guildestern.txt` use
    `49 FF 00` plus `4A FF ...` around repeated camera-oscillation beats.
  - `decoded_scripts/18-Limestone Quarry/318-The Fallen Hall.txt` and
    `321-Screams of the Wounded.txt` use the counted slot form
    `49 00 01` / `4A 00 8A 00`.
