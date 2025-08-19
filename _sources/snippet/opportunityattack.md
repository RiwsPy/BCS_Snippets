# ✅ Réaliser une attaque d'opportunité

Il s'agit d'une attaque de mêlée contre les personnages sans arme dans la main directrice (exceptés les moines) ou armés d'une arme à distance.

`````{tab-set}
````{tab-item} Classique
```vb.net
IF
    ...
    //'' je dois être équipé d'une arme de mêlée ou de mes poings si je suis Moine
    !IsWeaponRanged(Myself)
    OR(2)
        HasWeaponEquiped(Myself)
        Class(Myself, MONK)

    //'' ciblage
    See(NearestEnemyOf(Myself))

    //'' on évitera de venir au contact d'une créature dangereuse
    !StateCheck(LastSeenBy(Myself), STATE_NOT_APPROACHABLE)

    //'' on évitera de trop s'éloigner de sa position pour réaliser l'attaque
    //'' plus la distance est élevée moins cette attaque sera opportune
    OR(2)
        InWeaponRange(LastSeenBy(Myself))
        PersonalSpaceDistance(LastSeenBy(Myself), 6) //'' exemple

    //'' la cible est équipée d'une arme de jet ou n'a pas d'arme (et n'est pas Moine)
    OR(2)
        !HasWeaponEquiped(LastSeenBy(Myself))
        IsWeaponRanged(LastSeenBy(Myself))
    OR(2)
        !Class(LastSeenBy(Myself), MONK)
        IsWeaponRanged(LastSeenBy(Myself))
    ...

    //'' mon arme est en capacité de toucher la cible
    WeaponCanDamage(LastSeenBy(Myself), MAINHAND)
    WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
THEN
```
````
`````

```{note}
Le bonus est de +4 au toucher et aux dégâts.\
Lors d'une attaque sournoise, les dégâts sont pris en compte par le multiplicateur.
```