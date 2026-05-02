# ⚠️ Réaliser une attaque sournoise

Snippet qui cherche à savoir si le personnage est en mesure de réussir une attaque sournoise et d'infliger des dégâts.\
Dans le même temps, il cherche à gérer les sneak attacks.

`````{tab-set}
````{tab-item} Base
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)

    //# Dégâts bonus
    CheckStatGT(Myself, 1, BACKSTABDAMAGEMULTIPLIER)

    //# Arme compatible et utilisable par les voleurs
    !IsWeaponRanged(Myself)

    //# Contrainte d'invisibilité
    OR(4)
        StateCheck(Myself, STATE_INVISIBLE)
        CheckStat(Myself, 1, ASSASSINATE) //# BitCheck BIT0 and BIT1 instead
        CheckStat(Myself, 2, ASSASSINATE)
        False() //# Check 3E sneak attack

    //# Contrainte de position
    OR(4)
        Allegiance(Myself, NOTGOOD)
        CheckStat(Myself, 1, ASSASSINATE) //# BitCheck BIT0 and BIT2 instead
        CheckStat(Myself, 4, ASSASSINATE)
        False()  //# placement dans le dos nécessaire

    //# Recherche de cible…
THEN
```
````

````{tab-item} Ciblage
```cr
IF
    //# …
    See([ANYONE]) //# TODO

    CheckStat(LastSeenBy(Myself), 0, IMMUNITY_TO_BACKSTAB)
    !Kit(LastSeenBy(Myself), BARBARIAN)
    CheckStatLT(LastSeenBy(Myself), 1, STONESKINS)
    CheckStatLT(LastSeenBy(Myself), 1, STONESKINSGOLEM)
    WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
    WeaponCanDamage(LastSeenBy(Myself), MAINHAND)
THEN
```
La caractéristique `IMMUNITY_TO_BACKSTAB` protège contre les attaques sournoises et les sneak attacks.\
Les personnages de kit `BARBARIAN` sont également immunisés.\
On enlève aussi les personnages protégés de peaux de pierre ainsi que ceux qui ne peuvent être touchés par l'arme équipée.

Il s'agit des contraintes qui empêchent l'attaque de réussir.\
D'autres contraintes moins fortes peuvent être ajoutées comme `!StateCheck(LastSeenBy(Myself), STATE_MIRRORIMAGE)`.
````

````{tab-item} Bloc sans positionnement
```cr

IF
    ActionListEmpty()
    Detect(Myself)
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)

    //# Dégâts bonus
    CheckStatGT(Myself, 1, BACKSTABDAMAGEMULTIPLIER)

    //# Arme compatible et utilisable par les voleurs
    !IsWeaponRanged(Myself)

    //# Contrainte d'invisibilité
    OR(4)
        StateCheck(Myself, STATE_INVISIBLE)
        CheckStat(Myself, 1, ASSASSINATE) //# BitCheck BIT0 and BIT1 instead
        CheckStat(Myself, 2, ASSASSINATE)
        False() //# Check 3E sneak attack

    //# Absence de contrainte de position
    OR(3)
        Allegiance(Myself, NOTGOOD)
        CheckStat(Myself, 1, ASSASSINATE) //# BitCheck BIT0 and BIT2 instead
        CheckStat(Myself, 4, ASSASSINATE)

    //# Ciblage
    OR(7)
        !See(NearestEnemyOf(Myself))
        !CheckStat(LastSeenBy(Myself), 0, IMMUNITY_TO_BACKSTAB)
        Kit(LastSeenBy(Myself), BARBARIAN)
        !CheckStatLT(LastSeenBy(Myself), 1, STONESKINS)
        !CheckStatLT(LastSeenBy(Myself), 1, STONESKINSGOLEM)
        !WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
        !WeaponCanDamage(LastSeenBy(Myself), MAINHAND)
    //# …

    Detect(Myself)
    False()
THEN
    RESPONSE #0
END

IF
    ActionListEmpty()
    !Range(LastSeenBy(Myself), 0)
THEN
    RESPONSE #1
        AttackOneRound(LastSeenBy(Myself))
END

```
Dans ces conditions avantageuses, le personnage peut réussir une attaque sournoise ou une sneak attack sans avoir à se repositionner car l'angle d'attaque n'a aucun impact.

````


````{tab-item} Bloc avec positionnement
```cr
IF
    OR(2)
        ActionListEmpty()
        Trigger(32)
    Detect(Myself)
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)

    //# Dégâts bonus
    CheckStatGT(Myself, 1, BACKSTABDAMAGEMULTIPLIER)

    //# Arme compatible et utilisable par les voleurs
    !IsWeaponRanged(Myself)

    //# Contrainte d'invisibilité
    OR(4)
        StateCheck(Myself, STATE_INVISIBLE)
        CheckStat(Myself, 1, ASSASSINATE) //# BitCheck BIT0 and BIT1 instead
        CheckStat(Myself, 2, ASSASSINATE)
        False() //# Check 3E sneak attack

    //# Ciblage
    OR(7)
        !See(NearestEnemyOf(Myself))
        !CheckStat(LastSeenBy(Myself), 0, IMMUNITY_TO_BACKSTAB)
        Kit(LastSeenBy(Myself), BARBARIAN)
        !CheckStatLT(LastSeenBy(Myself), 1, STONESKINS)
        !CheckStatLT(LastSeenBy(Myself), 1, STONESKINSGOLEM)
        !WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
        !WeaponCanDamage(LastSeenBy(Myself), MAINHAND)
    //# …

    Detect(Myself)
    False()
THEN
    RESPONSE #0
END

//# Gestion du cas où le positionnement n'est pas requis
IF
    ActionListEmpty()
    !Range(LastSeenBy(Myself), 0)
    OR(3)
        Allegiance(Myself, NOTGOOD)
        CheckStat(Myself, 1, ASSASSINATE) //# BitCheck BIT0 and BIT2 instead
        CheckStat(Myself, 4, ASSASSINATE)
THEN
    RESPONSE #1
        AttackOneRound(LastSeenBy(Myself))
END

//# Gestion du cas où le positionnement est requis
IF
    ActionListEmpty()
    !Range(LastSeenBy(Myself), 0)
    !PersonalSpaceDistance(LastSeenBy(Myself), 3)
THEN
    RESPONSE #1
        MoveToObjectOffset(LastSeenBy(Myself), [-10.0]) //# on s'approche sans gêner la cible
        Continue()
    RESPONSE #1
        MoveToObjectOffset(LastSeenBy(Myself), [10.0])
        Continue()
    RESPONSE #1
        MoveToObjectOffset(LastSeenBy(Myself), [0.-10])
        Continue()
    RESPONSE #1
        MoveToObjectOffset(LastSeenBy(Myself), [0.10])
        Continue()
END

//# Moins contraignant, on rentrera forcément dedans après le Continue() du bloc précédent
//# On rentrera également dedans si la cible est à 3 pieds ou moins
IF
    ActionListEmpty()
    !Range(LastSeenBy(Myself), 0)
THEN
    RESPONSE #1
        SendTrigger(32)
        FollowObjectFormation(LastSeenBy(Myself), 10, 1) //# position quiconce arrière
END

IF
    !ActionListEmpty()
    Trigger(32)
    !Range(LastSeenBy(Myself), 0)
    !PersonalSpaceDistance(LastSeenBy(Myself), 3)
THEN
    RESPONSE #1
        AttackOneRound(LastSeenBy(Myself))
END

```

En cas de positionnement, deux passages de scripts sont requis. Le premier permet de se positionner, le second d'attaquer.\
Le `FollowObjectFormation` permet de se positionner derrière le personnage mais l'action n'a pas de limite de durée.\
La créature va donc "Follow" jusqu'au prochain passage de script, attention car `ActionListEmpty()` vaudra faux.\
Pendant ce second passage, le trigger 32 permet de savoir que le personnage devrait être placé derrière sa cible. Nous attaquons la cible compatible la plus proche, sans certitude qu'il s'agisse de la même cible.\
Le trigger peut être remplacé par une variable LOCALS.

````

`````

```{warning}
Plusieurs paramètres sont perfectibles :
- On ne peut assurer que l'arme actuelle soit compatible avec les attaques sournoises
- On ne sait pas si les sneak attacks sont activées ou non (les contraintes ne sont pas les mêmes)
- Seules les valeurs les plus simples de ASSASSINATE sont testées
```


## Réflexion

- `HasItemCategory` : on pourrait autoriser certains types d'arme sans être certain que cette arme soit vraiment compatible pour les voleurs, et cela pourrait retirer toutes les armes des autres categorie qui pourraient être compatible. Enfin, ça ne gère pas les ajouts de catégorie pour les mods.
- Avec `Class(Myself, THIEF)`, on peut imaginer qu'il utilise des armes pour voleur mais trop limitant. Pas adapté avec la HLA "Use Any Item".
- La contrainte `CheckStatGT(Myself, 1, BACKSTABDAMAGEMULTIPLIER)` est nécessaire pour les attaques sournoises mais est aussi un trick qui améliore la gestion des sneak attacks. Car en général, les personnages ayant un bonus pour les attaques sournoises, en ont également un pour les sneak attacks.
