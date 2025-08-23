---
myst:
  substitutions:
    OBJ_FILENAME: SPPR729
    SPELL: CLERIC_MASS_RAISE_DEAD
    SPELL_TYPE: DEFENSIVE
    TARGET: Myself
    SPELL_PRIEST_FAILURE: →
    SPELL_CHECK_WILDSURGE: →
---

# Résurrection de masse ({{ OBJ_FILENAME }})

Ce sort est un peu particulier car il est efficace sur tous les membres du groupe.\
Il soigne également de 3D10 points de vie. Mais soyons honnête : ça serait du gachis de l'utiliser que pour ça.

Une difficulté ici est que l'on ne peut pas se baser sur des [object], car les personnages sont morts.

`````{tab-set}
````{tab-item} Base
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    HaveSpell(CLERIC_MASS_RAISE_DEAD)
    OR(2)
        !CheckSpellState(Myself, DISEASED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    CheckStat(Myself, 0, CLERIC_INSECT_PLAGUE)

    NumInPartyGT(2)
    NumInPartyAliveLT(4)
    OR(2)
        NumInPartyGT(5)
        NumInPartyAliveLT(3)
    OR(2)
        NumInPartyGT(4)
        NumInPartyAliveLT(2)
    //# …
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, CLERIC_MASS_RAISE_DEAD)
END
```
Il est possible de connaître la taille du groupe ainsi que le nombre de membre encore en vie.\
Et sans pouvoir faire de soustraction, il est possible de faire des comparaisons.

Le sort est lancé si :
- il y a au moins 3 morts dans le groupe
- ou s'il n'y a qu'un survivant dans un groupe de 3
- ça gère même certain cas où il y aurait plus de 6 personnes dans le groupe
````

````{tab-item} Allié hors-combat
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    HaveSpell(CLERIC_MASS_RAISE_DEAD)
    OR(2)
        !CheckSpellState(Myself, DISEASED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    CheckStat(Myself, 0, CLERIC_INSECT_PLAGUE)

    !ActuallyInCombat()
    Allegiance(Myself, GOODCUTOFF)
    !See(NearestEnemyOf(Myself))

    NumInPartyGT(2)
    NumInPartyAliveLT(4)
    OR(2)
        NumInPartyGT(5)
        NumInPartyAliveLT(3)
    OR(2)
        NumInPartyGT(4)
        NumInPartyAliveLT(2)
    //# …
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, CLERIC_MASS_RAISE_DEAD)
END
```
- Aucun combat en cours
- Le lanceur de sort est un allié
````
`````
