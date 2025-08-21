# ⚠️ Réaliser une attaque sournoise

Snippet qui cherche à savoir si le personnage est en mesure de réussir une attaque sournoise et d'infliger des dégâts.

```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)

    // arme compatible et utilisable par les voleurs
    !IsWeaponRanged(Myself)

    OR(2)
        CheckStatGT(Myself, 1, BACKSTABDAMAGEMULTIPLIER)
        False() // Check 3E sneak attack + valeur (selon kit/class)

    // Contrainte d'invisibilité
    OR(4)
        StateCheck(Myself, STATE_INVISIBLE)
        CheckStat(Myself, 1, ASSASSINATE) // BitCheck BIT0 and BIT1 instead
        CheckStat(Myself, 2, ASSASSINATE)
        False() // Check 3E sneak attack

    See([ANYONE]) //# TODO

    // Contrainte de position
    OR(4)
        Allegiance(Myself, NOTGOOD)
        CheckStat(Myself, 1, ASSASSINATE) // BitCheck BIT0 and BIT2 instead
        CheckStat(Myself, 4, ASSASSINATE)
        False()  // placement dans le dos

    CheckStat(LastSeenBy(Myself), 0, IMMUNITY_TO_BACKSTAB)
    !Kit(LastSeenBy(Myself), BARBARIAN)
    CheckStatLT(LastSeenBy(Myself), 1, STONESKINS)
    CheckStatLT(LastSeenBy(Myself), 1, STONESKINSGOLEM)
    WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
    WeaponCanDamage(LastSeenBy(Myself), MAINHAND)
THEN
```

```{warning}
Beaucoup de paramètres sont manquants :
- On ne peut assurer que l'arme actuelle soit compatible avec les attaques sournoises
- On ne sait pas si les sneak attacks sont activées ou non (les contraintes ne sont pas les mêmes)
- On ne sait pas comment la créature est positionnée par rapport à l'adversaire 
- Seules les valeurs les plus simples de ASSASSINATE sont testées
```


## Réflexion

- `HasItemCategory` : on pourrait autoriser certains types d'arme sans être certain que cette arme soit vraiment compatible pour les voleurs, et cela pourrait retirer toutes les armes des autres categorie qui pourraient être compatible. Enfin, ça ne gère pas les ajouts de catégorie pour les mods.
- Avec `Class(Myself, THIEF)`, on peut imaginer qu'il utilise des armes pour voleur mais trop limitant. Pas adapté avec la HLA "Use Any Item".

