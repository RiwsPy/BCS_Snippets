# ✅ Can PickPocket

Snippet qui permet de savoir si le vol à la tire est envisageable ou non.

Utilisable quelque soit l'allégeance de la créature.\
Il n'est pas limité aux "Voleurs" par contre un minimum de maîtrise est nécessaire.



`````{tab-set}
````{tab-item} Minimale
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !ButtonDisabled(BUTTON_THIEVING)
    CheckStatGT(Myself, 0, PICKPOCKET) //# Facultatif mais conseillé
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    See([ANYONE]) //# TODO

    !CheckStat(LastSeenBy(Myself), 255, PICKPOCKET)
    OR(2) //# Un NOTEVIL ne peut voler un EVILCUTOFF et inversement
        Allegiance(Myself, NOTEVIL)
        Allegiance(LastSeenBy(Myself), NOTGOOD)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        Allegiance(LastSeenBy(Myself), NOTEVIL)
    HasItemType(LastSeenBy(Myself), DROPPABLE)

    //# …
THEN
    RESPONSE #1
        PickPockets(LastSeenBy(Myself))
END
```

La version minimale qui prévient les cas où le larçin sera impossible ou l'échec sera automatique.
````

````{tab-item} RP
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !ButtonDisabled(BUTTON_THIEVING)
    CheckStatGT(Myself, 9, PICKPOCKET) //# Facultatif mais conseillé
    !StateCheck(Myself, STATE_INVISIBLE) //# Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    !See(NearestEnemyOf(Myself))
    See([NEUTRAL])

    Range(LastSeenBy(Myself), 2)

    !CheckStat(LastSeenBy(Myself), 255, PICKPOCKET)
    HasItemType(LastSeenBy(Myself), DROPPABLE)

    //# …
THEN
    RESPONSE #1
        PickPockets(LastSeenBy(Myself))
END
```
- Pas de vol si un ennemi est à vue
- Une maîtrise de 10 en vol est requise (valeur minimale dans SLTSTEAL.2DA sur la version Vanilla)
- Blocage si certains State sont actifs
- On ne peut voler les ennemis et voler les alliés n'est pas encouragé, on ne vise alors que les neutres
````

````{tab-item} RP Safe
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !ButtonDisabled(BUTTON_THIEVING)
    CheckStatGT(Myself, 49, PICKPOCKET)
    !StateCheck(Myself, STATE_INVISIBLE) //# Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    !See(NearestEnemyOf(Myself))
    See([NEUTRAL])

    Range(LastSeenBy(Myself), 2)
    CheckStatLT(LastSeenBy(Myself), 200, PICKPOCKET)
    HasItemType(LastSeenBy(Myself), DROPPABLE)

    //# Check safe
    OR(2)
        CheckStat(LastSeenBy(Myself), 0, PICKPOCKET)
        CheckStatGT(Myself, 74, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 25, PICKPOCKET)
        CheckStatGT(Myself, 99, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 50, PICKPOCKET)
        CheckStatGT(Myself, 124, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 75, PICKPOCKET)
        CheckStatGT(Myself, 149, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 100, PICKPOCKET)
        CheckStatGT(Myself, 174, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 125, PICKPOCKET)
        CheckStatGT(Myself, 199, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 150, PICKPOCKET)
        CheckStatGT(Myself, 224, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 175, PICKPOCKET)
        CheckStatGT(Myself, 249, PICKPOCKET)

    //# …
THEN
    RESPONSE #1
        PickPockets(LastSeenBy(Myself))
END
```

La version RP mais en vérifiant d'avoir au minimum 50% de réussite.
````

````{tab-item} RP Safe+
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !ButtonDisabled(BUTTON_THIEVING)
    CheckStatGT(Myself, 99, PICKPOCKET)
    !StateCheck(Myself, STATE_INVISIBLE) //# Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    !See(NearestEnemyOf(Myself))
    See([NEUTRAL])

    Range(LastSeenBy(Myself), 2)
    CheckStatLT(LastSeenBy(Myself), 150, PICKPOCKET)
    HasItemType(LastSeenBy(Myself), DROPPABLE)

    //# Check safe
    OR(2)
        CheckStat(LastSeenBy(Myself), 0, PICKPOCKET)
        CheckStatGT(Myself, 124, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 25, PICKPOCKET)
        CheckStatGT(Myself, 149, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 50, PICKPOCKET)
        CheckStatGT(Myself, 174, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 75, PICKPOCKET)
        CheckStatGT(Myself, 199, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 100, PICKPOCKET)
        CheckStatGT(Myself, 224, PICKPOCKET)
    OR(2)
        CheckStatLT(LastSeenBy(Myself), 125, PICKPOCKET)
        CheckStatGT(Myself, 249, PICKPOCKET)

    //# …
THEN
    RESPONSE #1
        PickPockets(LastSeenBy(Myself))
END
```

La version RP Safe mais en vérifiant d'avoir 99% de réussite.
````
`````

## Limites

- Des objets peuvent être considérés comme volables mais placés dans des slots qui ne peuvent être volés (celui des bottes par exemple).

### Augmenter la distance

2 pieds est la distance maximale où le vol est possible. Au-delà, le personnage devra se déplacer (si c'est possible).\
Laisser cette contrainte c'est assurer l'efficacité du bloc mais 2 pieds c'est faible, et il sera tentant d'augmenter cette distance.\
Ce n'est pas sans conséquence.

Les personnages avec un grand `PersonalSpace` ne peuvent voler ou être volé car la distance n'est jamais inférieure ou égale à 2.\
Afin d'éviter que le personnage ne se bloque en cherchant (en vain) à voler une cible inatteignable, il est conseillé de scinder le bloc en deux :
1. Identique à ceux présentés plus haut, servent à utiliser l'action `PickPockets`
1. Gère une distance supérieure à 2 en utilisant `MoveToObject` mais surtout, les conditions de sortie

```cr
//# Bloc 1
//# ...

//# Bloc 2
IF
    //# ...

    !Range(LastSeenBy(Myself), 2) //# est au-delà de la distance maximale
    !PersonnalSpaceDistance(LastSeenBy(Myself), 1) //# condition de sortie
THEN
    RESPONSE #1
        MoveToObject(LastSeenBy(Myself))
END
```

En se déplaçant vers la cible, si les personnages sont "larges", on arrivera à PersonnalSpaceDistance inférieur à 1 et un Range supérieur à 2.\
Ainsi, aucun des deux blocs ne sera en mesure de s'exécuter.
