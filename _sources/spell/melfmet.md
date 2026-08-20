---
myst:
  substitutions:
    OBJ_FILENAME: SPWI325
    SPELL: WIZARD_MELF_METEOR
    SPELL_TYPE: OFFENSIVE
    TARGET: Myself
---

# Météore de Melf ({{ OBJ_FILENAME }})

Le sort de Minuscules météores de Melf est intéressant car il crée une arme magique entre les doigts du lanceur de sort, les vérifications sont inhabituelles.

Être équipé de météores n'a ni caractéristique ni spellstate particulier, il "suffit" de vérifier que des météores sont bien équipées.

À savoir que `HasItemEquiped` et `HasItemEquipedReal` ne vérifient pas le slot d'arme magique.

Bien que peu performants, `HasItem` et `NumItems` peuvent nous aider.

Mais il reste un problème : l'objet peut être dans le sac et non équipé.

`CheckItemSlot` nous permet de dépasser ces limites, en vérifiant spécifiquement la présence de l'objet à l'emplacement désiré.


`````{tab-set}
````{tab-item} Possession spécifique
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)            //# CowledWizard dont check this area
    Global("BDAI_DISABLE_OFFENSIVE", "LOCALS", 0)    //# Using OFFENSIVE magic is authorized
    HaveSpell(WIZARD_MELF_METEOR)
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

    //# Vérification
    !CheckItemSlot(Myself, "melfmet", SLOT_MISC19)

    //# …
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_MELF_METEOR)
END
```

Super, mais peut-être que l'on a envie de bloquer l'incantation du sort si une `Lame noire du désastre` est équipée ?\
Il faut donc lister toutes les armes possibles.

````

````{tab-item} Possession générique
```cr
IF
    ActuallyInCombat()
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)            //# CowledWizard dont check this area
    Global("BDAI_DISABLE_OFFENSIVE", "LOCALS", 0)    //# Using OFFENSIVE magic is authorized
    HaveSpell(WIZARD_MELF_METEOR)
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

    //# Vérification
    !HasItemSlot(Myself, SLOT_MISC19)

    //# …
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_MELF_METEOR)
END
```

Cette version générique retourne le problème en utilisant `HasItemSlot` : plutôt que de lister toutes les armes magiques de tous les mods, on vérifie si l'emplacement de l'arme magique est vide.\
Ainsi, on ne pourra pas invoquer une arme magique si l'on en possède déjà une.

````

`````