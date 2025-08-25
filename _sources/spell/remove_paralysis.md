---
myst:
  substitutions:
    OBJ_FILENAME: SPPR308
    SPELL: CLERIC_REMOVE_PARALYSIS
    SPELL_TYPE: DEFENSIVE
    TARGET: LastSeenBy(Myself)
---

# Délivrance de la paralysie ({{ OBJ_FILENAME }})

`````{tab-set}
````{tab-item} Classique
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREPRIEST)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    HaveSpell(CLERIC_REMOVE_PARALYSIS)

    See(NearestAllyOf(Myself))
    StateCheck(LastSeenBy(Myself), STATE_STUNNED | STATE_HELPLESS)
    !StateCheck(LastSeenBy(Myself), STATE_SLEEPING | STATE_NOT_APPROACHABLE | STATE_FEEBLEMINDED)
    OR(2)
        !StateCheck(LastSeenBy(Myself), STATE_IMPROVEDINVISIBILITY)
        !CheckStat(Myself, 0, SEEINVISIBLE)
    CheckStat(LastSeenBy(Myself), 0, WEB)
    !CheckStat(LastSeenBy(Myself), 2, PUPPETTYPE)
    CheckStat(LastSeenBy(Myself), 0, MINORGLOBE)
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(LastSeenBy(Myself), CLERIC_REMOVE_PARALYSIS)
END
```
````

````{tab-item} Bloc
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREPRIEST)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    HaveSpell(CLERIC_REMOVE_PARALYSIS)

    Detect(Myself)
    OR(6)
        !See(NearestAllyOf(Myself))
        !StateCheck(LastSeenBy(Myself), STATE_STUNNED | STATE_HELPLESS)
        StateCheck(LastSeenBy(Myself), STATE_SLEEPING | STATE_NOT_APPROACHABLE | STATE_FEEBLEMINDED | STATE_IMPROVEDINVISIBILITY)
        !CheckStat(LastSeenBy(Myself), 0, WEB)
        CheckStat(LastSeenBy(Myself), 2, PUPPETTYPE)
        !CheckStat(LastSeenBy(Myself), 0, MINORGLOBE)
    OR(6)
        !See(SecondNearestAllyOf(Myself))
        !StateCheck(LastSeenBy(Myself), STATE_STUNNED | STATE_HELPLESS)
        StateCheck(LastSeenBy(Myself), STATE_SLEEPING | STATE_NOT_APPROACHABLE | STATE_FEEBLEMINDED | STATE_IMPROVEDINVISIBILITY)
        !CheckStat(LastSeenBy(Myself), 0, WEB)
        CheckStat(LastSeenBy(Myself), 2, PUPPETTYPE)
        !CheckStat(LastSeenBy(Myself), 0, MINORGLOBE)
    OR(6)
        !See(ThirdNearestAllyOf(Myself))
        !StateCheck(LastSeenBy(Myself), STATE_STUNNED | STATE_HELPLESS)
        StateCheck(LastSeenBy(Myself), STATE_SLEEPING | STATE_NOT_APPROACHABLE | STATE_FEEBLEMINDED | STATE_IMPROVEDINVISIBILITY)
        !CheckStat(LastSeenBy(Myself), 0, WEB)
        CheckStat(LastSeenBy(Myself), 2, PUPPETTYPE)
        !CheckStat(LastSeenBy(Myself), 0, MINORGLOBE)
    OR(6)
        !See(FourthNearestAllyOf(Myself))
        !StateCheck(LastSeenBy(Myself), STATE_STUNNED | STATE_HELPLESS)
        StateCheck(LastSeenBy(Myself), STATE_SLEEPING | STATE_NOT_APPROACHABLE | STATE_FEEBLEMINDED | STATE_IMPROVEDINVISIBILITY)
        !CheckStat(LastSeenBy(Myself), 0, WEB)
        CheckStat(LastSeenBy(Myself), 2, PUPPETTYPE)
        !CheckStat(LastSeenBy(Myself), 0, MINORGLOBE)
    Detect(Myself)
    False()
THEN
    RESPONSE #0
END

IF
    !Range(LastSeenBy(Myself), 0)
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(LastSeenBy(Myself), CLERIC_REMOVE_PARALYSIS)
END
```
- On perd en finesse sur la gestion du `STATE_IMPROVEDINVISIBILITY` pour les personnages ayant `SEEINVISIBLE` actif
- L'utilisation de `EEex_SpellObjectOffset` résolverait ce problème
````
`````

Caractéristiques :
- Seulement en combat (en dehors, il suffit de patienter)
- Sort à incantation longue donc sensible au poison
- Sensible aux hiatus
- Longue portée, on peut toucher des alliés éloignés
- On ne guérit pas les personnages confus, charmés… ils sont moins dangereux immobilisés
- On ne guérit pas les personnages débilisés ou endormis car ils seront toujours immobilisés
- On ne cible pas les personnages avec un globe d'invulnérabilité (sans effet)
- On ne cible pas les personnages immobilisés par d'autres sources
- On ne peut se cibler soi-même car on ne peut lancer des sorts une fois immobilisé
- C'est un sort de zone : les effets de renvois, réflexions ou pièges n'offrent aucune protection


## Limites

- Un personnage endormi pour une durée permanente qui a été guéri du sommeil matchera avec les conditions (la guérison ne soigne pas du `STATE_HELPLESS`)


## Ciblage

Pour identifier les cibles qui peuvent être soignées nous avons
```cr
    StateCheck(LastSeenBy(Myself), STATE_STUNNED | STATE_HELPLESS)
```

Or, dans la plupart des mods, nous retrouvons
```cr
    OR(2)
        StateCheck(LastSeenBy(Myself), STATE_STUNNED)
        CheckStat(LastSeenBy(Myself), 0, HELD)
```

Les deux choix sont bons.\
`HELD` a l'avantage de ne pas matcher avec le sommeil.\
`STATE_HELPLESS` a l'avantage de matcher avec la [fausse pétrification](https://gibberlings3.github.io/iesdp/opcodes/bgee.htm#op109).

C'est assez marginal et le plus déterminant a été la simplicité du code.\
La première solution permet de générer un unique bloc pour gérer plusieurs cibles (voir l'onglet "Complète").\
Chose qui n'est pas possible avec la seconde.

## Faux positifs

Quelque soit le choix fait, il y a des faux positifs.
| Nom | Solution apportée
| ---- | --------- |
| Toile d'araignée (opcode 159) | `CheckStat(LastSeenBy(Myself), 0, WEB)` |
| Image projectée (opcode 236) | `!CheckStat(LastSeenBy(Myself), 2, PUPPETTYPE)` |
| Sommeil (opcode 39) | `!StateCheck(LastSeenBy(Myself), STATE_SLEEPING)` |
| Immobilisation (opcode 185) | Aucune |

## Notes

Pour gérer l'image projetée, `!CheckStat(LastSeenBy(Myself), 2, WIZARD_MISLEAD)` est un choix honorable mais moins robuste car il peut être modifié/supprimé par les moddeurs.
