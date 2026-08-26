# ✔️ Evil can sleep

Les personnages du groupe et les familiers peuvent dormir afin de se soigner, régénérer leurs sorts mais par défaut, pas leurs ennemis.

Voici une proposition de solution pour ces derniers.

`````{tab-set}
````{tab-item} Basique
```cr
IF
    !GlobalTimerNotExpired("BCS_RestTimer", "LOCALS")
    !StateCheck(Myself, STATE_REALLY_DEAD)
    !InPartyAllowDead(Myself)
    !Allegiance(Myself, FAMILIAR)
    !ActuallyInCombat()
    !Detect(NearestEnemyOf(Myself))
THEN
    RESPONSE #1
        Rest()
        SetGlobalTimer("BCS_RestTimer", "LOCALS", EIGHT_HOURS)
        //# Reset des autres variables
END
```
La solution la plus simple, en un seul bloc.\
Applique `Rest()` toutes les 8 heures hors combat, mais aussi lors de la création du personnage (ce qui peut modifier les choix des moddeurs).

Le snippet ne s'applique pas aux membres du groupe ou aux familiers qui passent par le processus traditionnel (`RestParty`).\
De plus, on évite de recharger en plein combat, ce qui serait pour le moins surprenant.
````

````{tab-item} 2 blocs
```cr
IF
    Global("BCS_RestTimer", "LOCALS", 0)
    !StateCheck(Myself, STATE_REALLY_DEAD)
    !InPartyAllowDead(Myself)
    !Allegiance(Myself, FAMILIAR)
    OR(2)
        ActuallyInCombat()
        Detect(NearestEnemyOf(Myself))
THEN
    RESPONSE #1
        SetGlobalTimer("BCS_RestTimer", "LOCALS", EIGHT_HOURS)
        Continue()
END

IF
    !Global("BCS_RestTimer","LOCALS",0)
    GlobalTimerExpired("BCS_RestTimer", "LOCALS")
    !StateCheck(Myself, STATE_REALLY_DEAD)
    !InPartyAllowDead(Myself)
    !Allegiance(Myself, FAMILIAR)
    !ActuallyInCombat()
    !Detect(NearestEnemyOf(Myself))
THEN
    RESPONSE #1
        Rest()
        SetGlobal("BCS_RestTimer", "LOCALS", 0)
        //# Reset des autres variables
END

```
Plus réaliste, ce snippet n'applique pas `Rest()` à la création du personnage et démarre le timer après avoir vu une situation de combat.

Contrairement à la plupart des snippets de ce genre, une seule variable n'est utilisée au lieu de deux.
````
`````

`Rest()` ne permet pas de soigner le personnage, pour cela, il est conseillé de lancer un sort comme `ApplySpell(Myself, RESTORE_FULL_HEALTH)` après `Rest()`.