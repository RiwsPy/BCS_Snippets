---
myst:
  substitutions:
    OBJ_FILENAME: SCRL1N
    TARGET: Myself
---

# ⚠️ Retranscrire un sort


`````{tab-set}
````{tab-item} V1
```cr
IF
    !ActuallyInCombat()
    ActionListEmpty()
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    Global("BDAI_DISABLE_ITEMS", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    HasItemEquiped("SCRL1N", Myself)

    OR(2)
        Class(Myself, MAGE_ALL)
        Class(Myself, BARD_ALL)
    OR(2)
        CheckStatGT(Myself, 8, INT) //# Sinon 0% de chance de réussite
        DifficultyLT(NORMAL) //# 100% de réussite
    !HaveKnownSpellRES("SCRL1N")
THEN
    RESPONSE #1
        UseItemAbility("SCRL1N", Myself, 0, 1)
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
END
```

- Pas générique…
- Une seule retranscription par round


````