# ✔️ Portée à distance


Être à distance de tir ne suffit pas pour s'assurer de pouvoir tirer un projectile sans subir un malus de toucher.


`````{tab-set}
````{tab-item} Base
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)

    IsWeaponRanged(Myself)
    See(NearestEnemyOf(Myself))
    InWeaponRange(LastSeenBy(Myself))
    OR(2)
        !PersonalSpaceDistance(LastSeenBy(Myself), 4)
        StateCheck(LastSeenBy(Myself), STATE_HELPLESS)
    OR(2)
        PersonalSpaceDistance(LastSeenBy(Myself), 32)
        StateCheck(LastSeenBy(Myself), STATE_HELPLESS)
THEN
```
````
`````