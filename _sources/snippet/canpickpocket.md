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
    !DisabledButton(BUTTON_THIEVING)
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
        PickPocket(LastSeenBy(Myself))
END
```

La version minimale qui prévient les cas où le larçin sera impossible ou l'échec sera automatique.
````

````{tab-item} RP
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !DisabledButton(BUTTON_THIEVING)
    CheckStatGT(Myself, 9, PICKPOCKET) //# Facultatif mais conseillé
    !StateCheck(Myself, STATE_INVISIBLE) //# Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    !See(NearestEnemyOf(Myself))
    See([NEUTRAL])

    Range(LastSeenBy(Myself), 2) //# Facultatif, à savoir que dans le cas contraire, le personnage se déplacera et devra donc pouvoir se déplacer
    !CheckStat(LastSeenBy(Myself), 255, PICKPOCKET)
    HasItemType(LastSeenBy(Myself), DROPPABLE)

    //# …
THEN
    RESPONSE #1
        PickPocket(LastSeenBy(Myself))
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
    !DisabledButton(BUTTON_THIEVING)
    CheckStatGT(Myself, 49, PICKPOCKET)
    !StateCheck(Myself, STATE_INVISIBLE) //# Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    !See(NearestEnemyOf(Myself))
    See([NEUTRAL])

    Range(LastSeenBy(Myself), 2) //# Facultatif, à savoir que dans le cas contraire, le personnage se déplacera et devra donc pouvoir se déplacer
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
        PickPocket(LastSeenBy(Myself))
END
```

La version RP mais en vérifiant d'avoir au minimum 50% de réussite.
````

````{tab-item} RP Safe+
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !DisabledButton(BUTTON_THIEVING)
    CheckStatGT(Myself, 99, PICKPOCKET)
    !StateCheck(Myself, STATE_INVISIBLE) //# Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !InventoryFull(Myself)

    !See(NearestEnemyOf(Myself))
    See([NEUTRAL])

    Range(LastSeenBy(Myself), 2) //# Facultatif, à savoir que dans le cas contraire, le personnage se déplacera et devra donc pouvoir se déplacer
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
        PickPocket(LastSeenBy(Myself))
END
```

La version RP Safe mais en vérifiant d'avoir 99% de réussite.
````
`````

## Limites

- Des objets peuvent être considérés comme volables mais placés dans des slots qui ne peuvent être volés (celui des bottes par exemple).
