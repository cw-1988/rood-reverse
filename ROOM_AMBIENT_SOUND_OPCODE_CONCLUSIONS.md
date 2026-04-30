# Room Ambient Sound Opcode Conclusions

This pass confirms battle-script opcode `0x7A` as
`SetRoomAmbientSoundSuspended`.

## Conclusion

- `7A 01` suspends the current room ambient sound by saving the active ambient
  id and swapping in `-1`.
- `7A 00` restores the saved ambient id and clears the suspended state.

## Evidence

- The matched dispatch table in
  `src/BATTLE/INITBTL.PRG/12AC.c` maps opcode `0x7A` to the handler now named
  `vs_battle_script_setRoomAmbientSoundSuspended`.
- The readable handler body in `src/BATTLE/BATTLE.PRG/4A0A8.c` stores a local
  latch, calls `func_800913BC(-1)` when suspending, and later calls
  `func_800913BC(savedId)` when resuming.
- The sound helper in `src/BATTLE/BATTLE.PRG/2842C.c` shows
  `func_800913BC(int arg0)` swaps the persistent ambient sound id and only
  plays a replacement when `arg0 >= 0`, which is exactly the save/clear/restore
  behavior the opcode needs.
- Decoded script usage in the workspace lines up with that behavior. Good
  samples are `MAP138`, `MAP062`, `MAP001`, and `MAP006`, where `7A 01`
  appears near cutscene takeover and `7A 00` appears during cleanup.
