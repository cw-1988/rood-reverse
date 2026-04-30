# SCREFF2 Param Pair Conclusions

This pass keeps battle-script opcode `0xED` conservative at the script-facing
level, but tightens the implementation wording around the persistent two-value
pair it drives inside `SCREFF2.PRG`.

## Conclusion

- `0xED` is the shared battle-script tween that writes a two-parameter pair
  into `SCREFF2.PRG`.
- The best safe internal wording for the destination is `param0` / `param1`,
  not a stronger visual label yet.
- The script-facing local name can stay `ScreenEffectParamPairTween`.

## Evidence

- The matched apply side in `src/BATTLE/BATTLE.PRG/4A0A8.c` updates the `0xED`
  tween state through the shared effect-tween helper, then forwards the live
  pair to `vs_screff2_setParamPair`.
- `src/GIM/SCREFF2.PRG/0.c` shows that setter doing exactly two writes to the
  persistent `SCREFF2` state and nothing broader.
- A fresh full decode of the local `Game Data/MAP` corpus found 10 current
  `0xED` uses across 4 rooms. All observed reset forms are `ED 40 00 00 00`,
  while the other first-parameter values stay clustered near that neutral
  value, so the first byte is clearly a fixed-point parameter but not yet a
  fully named visual control.

## Scope Guard

This pass justifies clearer internal names for the SCREFF2 destination pair. It
does not yet justify a stronger script-facing rename for opcode `0xED` or a
specific player-visible label for either destination field.
