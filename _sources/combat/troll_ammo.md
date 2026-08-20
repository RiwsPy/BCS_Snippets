---
myst:
  substitutions:
    TARGET: LastSeenBy(Myself)
---

# ⚠️ Finir un troll à distance

Certains mods proposent de s'équiper de munitions pour "terminer" les trolls tombés au sol.

## Contexte

Pour créer un script efficace pour achever les trolls, encore faut-il les connaître.\
Dans la dernière version, leur anneau active un sort (`TROLLREG`) lorsqu'ils passent sous la barre des 12 points de vie.\
Ils s'écroulent dans le coma pendant 20 secondes. Cela active les states `STATE_SLEEPING`, `STATE_HELPLESS` et le splstate `DOESNT_AWAKEN_ON_DAMAGE`. Leurs points de vie tombent à 1. Et ils obtiennent une immunité à nombreux dégâts.\
Vous avez 20 secondes avant qu'ils ne se relèvent, complètement guéris.

## Ciblage

Les trolls sont du genre :
- [X.MONSTER.TROLL.TROLL]
- [X.GIANTHUMANOID.TROLL.TROLL]
- [X.GIANTHUMANOID.TROLL.SPECTRAL_TROLL]

Le point commun est `[0.0.TROLL]`.

## Au sol

Comment savoir si un troll est au sol ?\
Plusieurs signaux nous le renseignent mais tous ne sont pas fiables : le sommeil est dissipable, les points de vie peuvent être soignés ou régénérés, l'immunité aux dégâts peut être naturelle ou abaissé par un objet, la possession de TROLLREG n'est pas assurée...\
Finalement c'est l'utilisation de MONHP1 qui est retenue. Cet objet met la stat `MINHITPOINTS` à 1. Cela fonctionnera très bien pour la version vanilla et plus largement car, on ne peut infliger des dégâts mortels à un personnage avec cette caractéristique.

Enfin, les trolls peuvent avoir des résistances élémentaires variables, il convient donc de tester cela avant de gaspiller des flèches de feu sur un troll immunisé au feu.


`````{tab-set}
````{tab-item} Avec switch
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)      //# Attacking is authorized
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    CanEquipRanged()
    HasItemCategory(Myself, BOW, FALSE)
    HasItemEquiped("AROW04", Myself)
    See(NearestEnemyOfType([0.0.TROLL]))
    CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
    CheckStatLT(LastSeenBy(Myself), 100, RESIST_ACID)
THEN
    RESPONSE #1
        XEquipItem("AROW04", Myself, SLOT_AMMO0, EQUIP)
        AttackOneRound(LastSeenBy(Myself))
END
```

La plus permissive des solutions : on peut switcher d'arme et de munitions.\
⚠️ Il n'est pas possible d'être certain d'être équipé d'un arc.

````

````{tab-item} Switch de munition
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)      //# Attacking is authorized
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    HasItemCategory(Myself, BOW, TRUE)
    HasItemEquiped("AROW04", Myself)
    See(NearestEnemyOfType([0.0.TROLL]))
    CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
    CheckStatLT(LastSeenBy(Myself), 100, RESIST_ACID)
THEN
    RESPONSE #1
        XEquipItem("AROW04", Myself, SLOT_AMMO0, EQUIP)
        AttackOneRound(LastSeenBy(Myself))
END
```

On ne switch pas d'arme, les conditions réclament qu'un arc soit réellement équipé.
````

````{tab-item} Sans switch
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)      //# Attacking is authorized
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    CurrentAmmo("AROW04", Myself)
    See(NearestEnemyOfType([0.0.TROLL]))
    CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
    CheckStatLT(LastSeenBy(Myself), 100, RESIST_ACID)
    WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
THEN
    RESPONSE #1
        AttackOneRound(LastSeenBy(Myself))
END
```

Ici le projectile est directement équipé, les vérifications sont donc plus simples et les actions aussi.\
En bonus, on peut vérifier que l'arme actuelle est suffisamment enchantée.\
Mais quelle est la probabilité que cela se produise ?
````

````{tab-item} Sans switch 2
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)      //# Attacking is authorized
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    OR(4)
        CurrentAmmo("AROW06", Myself)
        CurrentAmmo("AROW08", Myself)
        CurrentAmmo("BULL04", Myself)
        CheckItemSlot(Myself, "MELFMET", SLOT_MISC19)
    See(NearestEnemyOfType([0.0.TROLL]))
    CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
    CheckStatLT(LastSeenBy(Myself), 100, RESISTFIRE)
    WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
THEN
    RESPONSE #1
        AttackOneRound(LastSeenBy(Myself))
END
```

Comme la précédente mais on profite de la simplification des actions : on peut donc tester plusieurs projectiles à la fois !
````

````{tab-item} Sans switch 2 complète
```cr
IF
    Detect(Myself)
    ActionListEmpty()
    Global("BDAI_DISABLE_ATTACK", "LOCALS", 0)      //# Attacking is authorized
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)  //# ACTION actions are usable
    CheckStat(Myself, 0, CASTERHOLD)
    OR(4)
        CurrentAmmo("AROW06", Myself)
        CurrentAmmo("AROW08", Myself)
        CurrentAmmo("BULL04", Myself)
        CheckItemSlot(Myself, "MELFMET", SLOT_MISC19)
    OR(4)
        !See(NearestEnemyOfType([0.0.TROLL]))
        !CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
        !CheckStatLT(LastSeenBy(Myself), 100, RESISTFIRE)
        !WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
    OR(4)
        !See(SecondNearestEnemyOfType([0.0.TROLL]))
        !CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
        !CheckStatLT(LastSeenBy(Myself), 100, RESISTFIRE)
        !WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
    OR(4)
        !See(ThirdNearestEnemyOfType([0.0.TROLL]))
        !CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)
        !CheckStatLT(LastSeenBy(Myself), 100, RESISTFIRE)
        !WeaponEffectiveVs(LastSeenBy(Myself), MAINHAND)
    Detect(Myself)
    False()
THEN
    RESPONSE #0
END

IF
    !Range(LastSeenBy(Myself), 0)
THEN
    RESPONSE #1
        AttackOneRound(LastSeenBy(Myself))
END
```

````

`````

## Notes

Cela a plusieurs limites importantes :
1. devoir tester les projectiles un par un
1. connaître leurs identifiants (ceux des mods sont incompatibles)
1. connaître le type de dégât élémentaire qu'ils infligent (si des mods changent cela…)


## À savoir

- ⚠️ `WeaponCanDamage(LastSeenBy(Myself), MAINHAND)` renverra faux car une fois au sol, les trolls sont immunisés aux dégâts physiques
- Pas besoin de checker la distance minimale car aucun jet d'attaque n'est effectué


- `CheckStatLT(LastSeenBy(Myself), 1, MINHITPOINTS)` n'est pas très RP, on pourrait privilégier `StateCheck(LastSeenBy(Myself), STATE_SLEEPING)` qui est beaucoup plus visuel, les trolls étant immunisés au sommeil sauf pendant la période où ils sont au sol.
