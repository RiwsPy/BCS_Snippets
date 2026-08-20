---
myst:
  substitutions:
    OBJ_FILENAME: SPWI510
    SPELL: WIZARD_SPELL_IMMUNITY
    SPELL_TYPE: DEFENSIVE
    TARGET: Myself
---

# Immunité contre les sorts ({{ OBJ_FILENAME }})

L'immunité contre les sorts est intéressante sur un aspect : la sélection multiple de sort.\
Très user-friendly pour le joueur, épreuve impossible pour un script.\
Il convient donc de contourner la problématique.

Dans ce cas, il est nécessaire de créer un bloc pour chaque sort secondaire.

`````{tab-set}
````{tab-item} Option 1
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)            //# CowledWizard dont check this area
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)    //# Using DEFENSIVE magic is authorized
    HaveSpell(WIZARD_SPELL_IMMUNITY)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)         //# failure rate less than 50%
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")   //# BD_Cast is expired
        !CheckStat(Myself, 0, AURACLEANSING)          //# or Auracleansing is active
    OR(2)                                             //# not poisoned or immune to poison
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)                                             //# no surge or have a protection against it
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    !CheckStat(Myself, 1, WIZARD_SPELL_IMMUNITY)

    //# …
THEN
    RESPONSE #1
        RemoveSpell(Myself, WIZARD_SPELL_IMMUNITY)
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        ForceSpell(Myself, WIZARD_SPELL_IMMUNITY_ABJURATION)
END
```

On enlève d'abord le sort principal puis on force le sort secondaire.
````

````{tab-item} Option 2
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)            //# CowledWizard dont check this area
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)    //# Using DEFENSIVE magic is authorized
    HaveSpell(WIZARD_SPELL_IMMUNITY)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)         //# failure rate less than 50%
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")   //# BD_Cast is expired
        !CheckStat(Myself, 0, AURACLEANSING)          //# or Auracleansing is active
    OR(2)                                             //# not poisoned or immune to poison
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)                                             //# no surge or have a protection against it
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    !CheckStat(Myself, 1, WIZARD_SPELL_IMMUNITY)

    //# …
THEN
    RESPONSE #1
        RemoveSpell(Myself, WIZARD_SPELL_IMMUNITY)
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        SpellNoDec(Myself, WIZARD_SPELL_IMMUNITY_ABJURATION)
END
```

On enlève d'abord le sort principal puis on lance "normalement" le sort secondaire.
````

````{tab-item} Option 3
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)            //# CowledWizard dont check this area
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)    //# Using DEFENSIVE magic is authorized
    HaveSpell(WIZARD_SPELL_IMMUNITY)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)         //# failure rate less than 50%
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")   //# BD_Cast is expired
        !CheckStat(Myself, 0, AURACLEANSING)          //# or Auracleansing is active
    OR(2)                                             //# not poisoned or immune to poison
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)                                             //# no surge or have a protection against it
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    !CheckStat(Myself, 1, WIZARD_SPELL_IMMUNITY)

    //# …
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_SPELL_IMMUNITY)
        ForceSpell(Myself, WIZARD_SPELL_IMMUNITY_ABJURATION)
END
```

On lance d'abord le sort principal puis on force le sort secondaire.\
On doit forcer le sort secondaire sinon il faut attendre un round avant de le lancer.
````

````{tab-item} Option 4
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)            //# CowledWizard dont check this area
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)    //# Using DEFENSIVE magic is authorized
    HaveSpell(WIZARD_SPELL_IMMUNITY)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)         //# failure rate less than 50%
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")   //# BD_Cast is expired
        !CheckStat(Myself, 0, AURACLEANSING)          //# or Auracleansing is active
    OR(2)                                             //# not poisoned or immune to poison
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)                                             //# no surge or have a protection against it
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    !CheckStat(Myself, 1, WIZARD_SPELL_IMMUNITY)

    //# …
THEN
    RESPONSE #1
        RemoveSpell(Myself, WIZARD_SPELL_IMMUNITY)
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        ForceSpell(Myself, WIZARD_SPELL_IMMUNITY)
        SpellNoDec(Myself, WIZARD_SPELL_IMMUNITY_ABJURATION)
END
```

On enlève d'abord le sort principal et on le force puis on lance "normalement" le sort secondaire.
````
`````

## Comparatif

Le sort 1 étant le sort principal (ici `WIZARD_SPELL_IMMUNITY`).\
Le sort 2 étant le sort secondaire (ici `WIZARD_SPELL_IMMUNITY_ABJURATION`).

| Effet | Effet original | Option 1 | Option 2 | Option 3 | Option 4
| - | - | - | - | - | - |
| Sort 1 : temps d'incantation | ✅ | ❌ | ❌ | ✅ | ✅ |
| Sort 1 : échec concentration | ✅ | ❌ | ❌ | ❓$^($$^1$$^)$ | ❌ |
| Sort 1 : impact SPELLFAILUREMAGE | ✅ | ❌ | ❌ | ❓$^($$^1$$^)$ | ❌ |
| Sort 1 : log de combat | ✅ | ❌ | ❌ | ✅ | ✅ |
| Sort 1 : effets visuels et sonores | ✅ | ❌ | ❌ | ✅ | ✅ |
| Sort 1 : SpellCast possible | ✅ | ❌ | ❌ | ✅ | ✅ |
| Sort 2 : temps d'incantation | ✅ | ✅ | ✅ | ✅ | ✅ |
| Sort 2 : échec concentration | ✅ | ❌ | ✅ | ❌ | ✅ |
| Sort 2 : impact SPELLFAILUREMAGE | ✅ | ❌ | ✅ | ❌ | ✅ |
| Sort 2 : log de combat | ✅ | ✅ | ✅ | ✅ | ✅ |
| Sort 2 : effets visuels et sonores | ✅ | ✅ | ✅ | ✅ | ✅ |
| Sort 2 : SpellCast possible | ✅ | ✅ | ✅ | ✅ | ✅ |
| Sort 2 : Contrainte de distance | ✅ | ❌ | ✅ | ❌ | ✅ |
| Effet dupliqué possible si joueur | ❌ | ❌ | ❌ | ✅ | ✅ |

$^($$^1$$^)$ : le sort peut échouer mais n'empêche pas de lancer le sort secondaire…

Par défaut, l'option 4 semble être la plus adaptée pour les personnages NOTGOOD.\
L'option 2 est moins réaliste mais plus générique.
