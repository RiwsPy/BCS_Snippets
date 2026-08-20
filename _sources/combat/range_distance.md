# ✔️ Portée à distance


Être à distance de tir ne suffit pas pour s'assurer de pouvoir tirer un projectile sans subir un malus de toucher.\
Il y a un malus (-8) si la cible est à une distance de moins de 4 et un malus (-2) si la cible est à une distance de plus de 32.\
Notez qu'aucun jet de toucher n'est effectué sur les cibles `STATE_HELPLESS`.


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

La limite de tir est le champ visuel de la créature active.
````

````{tab-item} Limitless
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)

    IsWeaponRanged(Myself)

    //# target is attackable, Player1 for example
    InMyArea(Player1)
    !StateCheck(Player1, STATE_REALLY_DEAD)
    IsActive(Player1)
    OR(2)
        !StateCheck(Player1, STATE_INVISIBLE)
        !CheckStat(Myself, 0, SEEINVISIBLE)
    OR(2)
        CheckStat(Player1, 0, SANCTUARY)
        !CheckStat(Myself, 0, SEEINVISIBLE)

    OR(2)
        !PersonalSpaceDistance(Player1, 4)
        StateCheck(Player1, STATE_HELPLESS)
    OR(2)
        PersonalSpaceDistance(Player1, 32)
        StateCheck(Player1, STATE_HELPLESS)
THEN
```

Il est possible de viser au-delà du champ visuel de la créature active si la portée de l'arme le permet mais cela nécessite de ne pas utiliser `See`.\
De plus, `InWeaponRange` renvoie faux si la cible est hors champ.\
Les objets comme `PlayerX` peuvent répondre à la problématique du `See`.\
Contrairement à la solution précédente, si la cible est hors portée de l'arme, le personnage se déplacera.
````


`````