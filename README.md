# Of Swords and Damagings

A complete guide on Witcher 3 sword damage calculation

## Highlights

- Euphoria increases only attack power, not total damage, critical damage is not multiplied
- Synergy only affects activated skills, inaccurate as shown on the in-game Character screen

## Abstract

- An interactive swords damage calculator is provided with mechanics explained
- Ability/mutation/decoction/bonus effectiveness are discussed
- Advices on general sword damage strategies are discussed

## Introduction

The motivation behind is to select the best ability/mutation/decoction/bonus when compromise are necessray. Sword strategy with hightest damage is preferred under high difficulty.

## State of the art

The most detailed damage mechanics is provided by almahnsor [^almahnsor2017], regular of forums.cdprojektred.com.

> The average damage is
> mWD = (maxWeaponDMG + minWeaponDMG) / 2
> 
> The critical damage is calculated like this:
> CHD = BD + mWD * (1 + AP + BCD + CD)
> with
> BCD = 25%
> BD = 0 at lv1 and 77 at lv100 (the gains are not linear and they decrease with the level)

ninjasam [^ninjasam2016] gave additional information on strong attack factor.

> The AttackTypeFactor for fast attacks is 1 and for strong attacks 11/6 or 1,83.

Boostedanimal161 [^Boostedanimal1612020] made some summerization of above but did not verify the result, the result is not structured and very confusing due to inconsistant terms.

All above does not give direct idea how effecitve a specific bonus is, as there are too much to be discussed under different conditions.

## Methodology

Compare formula results with DPS panel, as well as actual in game performace in some cases (e.g. Euphoria).

## Result

```python
import math

## attack power
ap = (
  (
    0
    ## mutagen, accurate without Synergy (with Greater red mutations, +10/20/30/40% depending on activated red skills)
    ## with 11 red skills and one Cat/Griffin/Bear School Techniques
    # + 1.5
    ## I use 4 alchamy skills (total 5 including the duplicate of the Acquired Tolerance), others being Synergy, Killing Spree, Protective Coating. Last one can be switched to red skills to achieve above
    + 1.4
    ## Synergy, inaccurate in Character screen, acutal value is 0.05 per red skill activated by Greater red mutations
    ## with 11 red skills and one cat school technique
    # + 0.05 * 11
    ## with 10 red skills, see above
    + 0.05 * 10
    ## Strength Training / Muscle Memory
    + 0.25
    ## Grandmaster Feline set
    # + 0.55
    ## Grandmaster Legnedary Feline set
    # + 0.68
  )
  * (
    ## base multiplier
    1
    ## contrary to the in game description, Euphoria is multiplied on attack power, not damage
    ## Euphoria, before 4.0.0, max toxity 100+167=267, 4 decoctions (incluidng Basilisk decoction with 40 toxity), +250%*0.75
    # + 1.875
    ## Euphoria, before 4.0.0, with fast matabolism and manticone gears (can be replaced after decoction consumption), max toxity 100+167+60=327, 5 decoctions (incluidng Basilisk decoction with 40 toxity), before next gen, +320%*0.75
    # + 2.4
    ## Euphoria, before 4.0.0, with double Acquired Tolerance, max toxity 100+167*2=434, 6 decoctions, +420%*0.75
    + 3.15
    ## Euphoria, before 4.0.0, with double Acquired Tolerance, with Fast Matabolism and Manticone set (can be replaced after decoction consumption), max toxity 100+167*2+60=494, 7 decoctions, +490%*0.75
    # + 3.675
    ## Euphoria, after 4.0.0, to ensure Water Hag decoction, max toxity (100+84)*0.8=147.2, 2 decoctions, +100%*0.75
    # + 0.75
    ## Euphoria, after 4.0.0, to ensure Water Hag decoction, with fast matabolism, max toxity (100+84+30)*0.8=171.2, 3 decoctions, +150%*0.75, note this need to carry additional Hightened Tolerance and Fast Matablism (but without the duplicated acquired tolerance)
    # + 1.125
    ## Euphoria, after 4.0.0, with double Acquired Tolerance, to ensure Water Hag decoction, max toxity (100+84*2)*0.8=214.4, 4 decoctions, +200%*0.75, note this need to carry additional Hightened Tolerance
    # + 1.5
    ## Euphoria, after 4.0.0, with double Acquired Tolerance, with Fast Matabolism and Manticone set (can be replaced after decoction consumption), max toxity 100+84*2+60=328, 6 decoctions, +300%*0.75, note that Water Hag decoction is hard to maintain on such condition, and 1.5*1.5=2.25, making this option meaningless
    # + 2.25
  )
)

## critical damage
cd = (
  0
  ## Precise Blows / Crushing Blows
  + 0.75
  ## Cat School Techniques
  # + 1
  ## Hjalmar's Steel Sword
  + 2
  ## Toussaint Knight's Steel Sword
  # + 1
  ## Nilfgaardian Gauntlets
  # + 0.5
)

## base critical damage multiplier, always apply
bcd = 0.25

## mean weapon damage
## Hjalmar's Steel Sword, level 99, 200% critical bonus, best steel sword on 1st run
mwd = 825
## Toussaint Knight's Steel Sword, level 148, best fast attack steel sword on NG+
# mwd = 1260
## Iris, level 137, best strong attack steel sword on NG+
# mwd = ?
## Aerondight, all time best silver sword
# mwd = 1120

## base damage, at level 100
bd = 77

print("weapon dmg modifier:   ", 1+ap)
print("critical dmg modifier: ", 1+ap+bcd+cd)

## hit damage
hd = bd+mwd*(1+ap)
## fast hit damage
print("fast:                  ", math.floor(hd))
## strong hit damage
print("strong:                ", math.floor(hd*1.833))
## critical fast hit damage
chd = bd+mwd*(1+ap+bcd+cd)
print("critical fast:         ", math.floor(chd)-1)
## critical strong hit damage
print("critical strong:       ", math.floor((math.floor(chd)-1)*1.833))
```

## Discussion

### Additional multipliers worth mentioning

- `Aerodight` *2 after fully charged
- `Iris` *2 with strong attack
- `Water Hag decoction` *1.5, need full vitality
- superior oil *1.5
- grindstone *1.2
- `Cat School Techniques`, fast attack damage *1.2
- `Bear School Techniques`, strong attack damage *1.2 though with significant stamina regen problems

### Resistance worth mentioning

- 99% is the maximum, significantly stronger near 99%
- `Undvik` set for general protection with emphasis on against human, `Ursine` set for general protection with emphasis on against monster, `Tousissant knight's tourney` set for protection against human only
- `Protective Coating` gives 25%/15% before/after 4.0.0, which is a significant nerf
- `Arachas decoction` required after 4.0.0 in most situations to get near to 99%

### Swords worth mentioning

- forged/fixed-leveled sword (like you always get `Iris` at level +37) will be capped at certain level (level 38 for `Iris`) on 1st run, thus less preferred when you reach higher levels on 1st run
- forged/fixed-leveled sword can reach beyond level 100 on ng+, making them highly preferred on NG+. To maximize the effect, you will have to start NG+ at level 100
- Why start NG+ at level 100? Because when you finally reach level 100, you want to face the guards with a level 148 `TKSS` rather than a lower level one
- `Aerodight` will strike twice after charged, making it the best silver (when charged) of all time. Rending charges 2 stacks per hit. Rending on 10 stacks grants two permant damage instead of 1.
- Any high level silver sword has a potential to better `Aerodight` in the first few strikes, but as none silver sword can achieve level 200 stats, Aerogight is always better after that
- `Hjalmar's sword` (level 99, 200% crit dmg) has the highest critical damage bonus, making it the best steel on 1st run. However, you will have to reach level 100 before `King's Gambit` when you acquire this sword. On NG+, this wil be outscaled as one can have swords beyond level 100, and based damage scales fast under Euphoria.
- `Iris` will strike twice when using strong attacks (including rend), making it the best steel to be used with strong attacks on NG+ being level 137, as none steel sword can achieve level 274 stats
- `Toussaint Knight's Steel Sword`, level 148, best for fast attacks on NG+

### Abilities worth mentioning

- `Acquired Tolerance`, for Euphoria
- `Synergy`, gives +0.5 (multiplied by euphoria)
- `Killing Spree`, ensures critical, +2
- `Protective Coating`, must have on death march, with Undvik armor or ursine armor for highest resistance. This skill does not work with the double skill glitch. Significantly nerfed after 4.0.0, now 99% resistance would require not only `Protective Coating` but also Arachas decoction with limited weight
- `Hightened Tolerance`, not necessary before 4.0.0, avoid health drop after 4.0.0 (with limited toxity), ensures Water hag decoction's function, *1.5
- `Cat School Techniques`, +1, at the expense of -0.15 (multiplied by euphoria) of a potential red skill, and *1.2 on fast damage, always worth it; Griffin School Techiniques still competitive though
- `Fast metabolism`, only required before consuming decoctions, necessary to be constant active after 4.0.0 in some situations
- `Euphoria` is significantly nerfed after 4.0.0, not only in terms of buff intensity but also requires additional skills to ensure 100% vitality / Water Hag decoction

### Game version worth mentioning

- 4.0.0 nerfed `Euphoria`, `Protective Coating`, toxity, and disabled continuous rend, making the game much harder on Death March, both in terms of damage output and tanking capbility. Strange decisions on a single player game. I'd suggest play on earlier verions, and you can always upgrade.

### General notes

- double `Acquired Tolerance`? [Glitch](https://www.youtube.com/watch?v=NhcWK4XNHvI)! Sorry I cannot find a non-video version that makes the process clear enough.
- `Hjalmar's Steel Sword` with level 99 and 200% critical damage? You need to [spam save load to acquire this](https://gamefaqs.gamespot.com/boards/702760-the-witcher-3-wild-hunt/78453604). I made it using an [AHK script](https://github.com/mkmark/ahk-witcher3-hjalmars-steel-sword) (70 times before getting the first 'perfect' sword).
- [A note on damage resistance and how it can stack to 99% : witcher](https://www.reddit.com/r/witcher/comments/4vx2on/a_note_on_damage_resistance_and_how_it_can_stack/)
- experience grind for level 100?
  - Of swords and dumplings, side quest objective, 40 xp per run, need manul operation, https://www.youtube.com/watch?app=desktop&v=UL0eJrcEnKc
  - drowner, 3xp per hit, need manul operation,  https://www.youtube.com/watch?app=desktop&v=UDRwG_LlAqs
  - `King's gambit` fist fight, side quest objective, 40 xp per run, can be automated using AHK script. Note that you actually need level 100 right before this for the perfect Hjalmar's sword, so unless you don't want it...
  - writh farming, need 4.0.0, piercing cold, need manual operation, https://www.reddit.com/r/Witcher3/comments/10ckih6/found_a_new_way_to_xp_farm_12k_xp_per_hour_in/
  - Cheat Engine if you don't care
    - https://fearlessrevolution.com/viewtopic.php?t=24169
    - https://fearlessrevolution.com/viewtopic.php?t=208

## Reference

[^almahnsor2017]: Damage Calculation | Forums - CD PROJEKT RED, https://forums.cdprojektred.com/index.php?threads/damage-calculation.94843/

[^ninjasam2016]: Damage calculation - The Witcher 3: Wild Hunt, https://gamefaqs.gamespot.com/boards/702760-the-witcher-3-wild-hunt/72428206

[^Boostedanimal1612020]: Damage Calcuation : Witcher3, https://www.reddit.com/r/Witcher3/comments/gtihgf/damage_calcuation/
