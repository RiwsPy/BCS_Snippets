---
myst:
  substitutions:
    OBJ_FILENAME: SPWI408
    SPELL: WIZARD_STONE_SKIN
    SPELL_TYPE: DEFENSIVE
    TARGET: Myself
    SPELL_PRIEST_FAILURE: →
    SPELL_CHECK_WILDSURGE: →
---

# Peau de pierre ({{ OBJ_FILENAME }})

Le sort `Peau de pierre` est l'un des plus usités en magie profane.\
Ce qu'on oublie souvent, c'est qu'il existe *deux* types de peau :
1. Peau de pierre
2. Peau de golem

Certes, la seconde n'est que peu utilisée mais ce n'est pas une raison pour l'ignorer.\
En revanche, si posséder plusieurs peaux d'un même type est utile, cumuler les différents types de peaux crée une sensibilité aux dissipations de la magie.\
On prendra donc garde de ne pas les cumuler inutilement.

Le temps d'incantation très faible du sort, le rend utilisable dans des conditions habituellement peu favorables aux sorts de niveau 4.\
On différencie ici, plusieurs cas :
- **Classique** : Pour ceux qui accepte de lancer une peau de pierre avant les combats. Vu sa durée, ce n'est pas une mauvaise pratique. S'il n'y a aucun combat, on exigera des condtions "parfaites" pour lancer le sort. La version combat ne permet de lancer le sort qu'en combat.
- **Économique** : Faut-il lancer une Peau pour faire face à deux gobelins ? Ici, on attend de subir des dégâts physiques pour se protéger. Cette version est intéressante pour les mages qui peuvent encaisser une attaque et qui doivent enchaîner les combats sans pouvoir se reposer.
- **Continuité** : En combat, une continuité des Peau de pierre est importante car elle assure une protection contre les attaques physiques mais aussi contre l'empoisonnement, deux facteurs impactant très négativement les lanceurs de sorts. Utile pour les mages PNJ qui partent du principe que ce combat pourrait être leur dernier.


## Propositions

`````{tab-set}
````{tab-item} Classique
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        See(NearestEnemyOf(Myself))
        CheckStat(Myself, 0, SPELLFAILUREMAGE)
    OR(3)
        See(NearestEnemyOf(Myself))
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    HaveSpell(WIZARD_STONE_SKIN)

    CheckStatLT(Myself, 1, STONESKINS)
    CheckStatLT(Myself, 1, STONESKINSGOLEM)
    !ModalState(STEALTH)
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_STONE_SKIN)
END
```

Cette version propose de lancer le sort même hors combat, dès que les peaux s'estompent.\
Comme sa durée est longue, il est utilisable à titre préventif.

Conditions :
* Aucun combat est en cours avec probabilité de lancer avec taux de réussite maximal
* Ou combat en cours avec probabilité de lancer avec taux de réussite acceptable
* Furtivité inactive (pour éviter de l'interrompre)
* Aucune peau restante
* Gestion des zones anti-magie d'Athkatla
````

````{tab-item} Classique en combat
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    HaveSpell(WIZARD_STONE_SKIN)

    CheckStatLT(Myself, 1, STONESKINS)
    CheckStatLT(Myself, 1, STONESKINSGOLEM)
    !ModalState(STEALTH)
    See(NearestEnemyOf(Myself))
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_STONE_SKIN)
END  
```

C'est la même que la précédente mais on ne l'active que si on voit un ennemi.

Conditions :
* Combat en cours avec probabilité de lancer avec taux de réussite acceptable
* Furtivité inactive (pour éviter de l'interrompre)
* Aucune peau restante
* Gestion des zones anti-magie d'Athkatla
````


````{tab-item} Économique
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    HaveSpell(WIZARD_STONE_SKIN)

    CheckStatLT(Myself, 1, STONESKINS)
    CheckStatLT(Myself, 1, STONESKINSGOLEM)
    OR(4)
        HitBy([ANYONE], PIERCING)
        HitBy([ANYONE], MISSILE)
        HitBy([ANYONE], SLASHING)
        HitBy([ANYONE], STUNNING)
        //# -_-'
        //# HitBy([ANYONE], CRUSHING)
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_STONE_SKIN)
END
```

Cette version propose d'attendre de subir une attaque susceptible d'être bloquée par une peau avant de réagir.\
Cela permet d'éviter de lancer ce sort :
- lors de combat simple ou si le personnage n'est pas attaqué
- suite à des dégâts d'un sort offensif contre lesquels les peaux sont inutiles

⚠️ Les dégâts contondants ne fonctionnent pas.\
`HitBy([ANYONE], CRUSHING)` prend en compte tous les types de dégâts et pas seulement les contondants.

Il n'est pas possible non plus d'utiliser :
- AttackedBy([ANYONE], MELEE)
- !HitBy([ANYONE], ...)

En ajoutant des contraintes sur les points de vie, il est possible de la cumuler la version `Continuité`.\
Par exemple, réserver celle-ci si on a plus de 50% de santé et basculer sur l'autre sinon.

Conditions :
* Un combat est en cours
* Probabilité de lancer avec taux de réussite acceptable (plus de 50%)
* Aucune peau restante
* La créature a subit des dégâts physiques lors du dernier passage de script
* Gestion des zones anti-magie d'Athkatla
````

````{tab-item} Continuité
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    HaveSpell(WIZARD_STONE_SKIN)

    CheckStatLT(Myself, 2, STONESKINS)
    CheckStatLT(Myself, 2, STONESKINSGOLEM)
    AttackedBy([ANYONE], DEFAULT)
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(Myself, WIZARD_STONE_SKIN)
END
```

Cette version assure la continuité entre les peaux de pierre.\
L'idée principale est de ne pas attendre de subir des dégâts pour réagir.\
Ni de lancer un autre sort alors qu'il ne reste plus qu'une peau, ce qui pourrait laisser le mage sans défense pendant près d'un round.\
Une seule attaque peut être fatale : peu de points de vie, attaque sournoise ou empoisonemment...

Conditions :
* A été attaqué récecemment (sans forcément avoir été touché)
* Probabilité de lancer avec taux de réussite acceptable (plus de 50%)
* 1 peau restante maximum (on pourrait alors en perdre une tout au plus)
* Gestion des zones anti-magie d'Athkatla
````
`````


```{note}
Une valeur négative dans `STONESKINS` ou `STONESKINSGOLEM` ne protège pas la créature, d'où le `CheckStatLT(Myself, 1, STONESKINS)`.
```