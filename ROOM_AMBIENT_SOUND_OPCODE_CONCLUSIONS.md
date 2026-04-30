# Room Ambient Sound Opcode Conclusions

This pass narrows battle-script opcode `0x7A` to the room ambient-sound
suspend/restore path without overclaiming its scope.

## Conclusion

- Best safe local name: `SetRoomAmbientSoundSuspended`
- `7A 01` latches a suspended state and, on the currently proven persistent
  ambient-id path, saves the active ambient id and swaps in `-1`.
- `7A 00` restores that saved ambient id and clears the suspended state when
  the same path is active.

## Evidence

- The matched dispatch table in
  `src/BATTLE/INITBTL.PRG/12AC.c` maps opcode `0x7A` to the handler now named
  `vs_battle_script_setRoomAmbientSoundSuspended`.
- The readable handler body in `src/BATTLE/BATTLE.PRG/4A0A8.c` stores a local
  latch, gates its save/restore work through `func_8008E470()`, calls
  `func_800913BC(-1)` when suspending, and later calls
  `func_800913BC(savedId)` when resuming.
- `func_8008E470()` in `src/BATTLE/BATTLE.PRG/146C.c` returns
  `(u_int)vs_battle_roomData.section14 < 1`, so the proven swap/restore path is
  explicitly conditional rather than unconditional.
- The sound helper in `src/BATTLE/BATTLE.PRG/2842C.c` shows
  `func_800913BC(int arg0)` swaps the persistent ambient sound id and only
  plays a replacement when `arg0 >= 0`, which is exactly the save/clear/restore
  behavior the opcode needs.
- The same room-audio code also has a separate AKAO-backed `section14` path, so
  the current proof is strongest for the persistent ambient-id fallback path.
- Decoded script usage in the workspace lines up with that behavior. Good
  samples are `MAP138`, `MAP062`, `MAP001`, and `MAP006`, where `7A 01`
  appears near cutscene takeover and `7A 00` appears during cleanup.
