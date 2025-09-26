# ✔️ Can Turn

`````{tab-set}
````{tab-item} Base
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 4)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    !ModalState(TURNUNDEAD)
    !CheckStat(Myself, 0, TURNUNDEADLEVEL)
THEN
    RESPONSE #1
        Turn()
END
```
Une fois de plus, il n'y a pas de restrictions de classe ou de kit.\
La condition de posséder un niveau de `TURNUNDEADLEVEL` plus le fait que le joueur ait choisi cette modale via `BDAI_SKILL_MODE`, ne laisse pas de place au doute.\
Pas non plus besoin de bloquer spécifiquement un kit sous prétexte qu'il ne peut pas repousser les morts-vivants comme l'Inquisiteur. La contrainte `!ButtonDisabled(BUTTON_TURNUNDEAD)` est suffisante et beaucoup plus générique.

Pour information, l'Inquisiteur a le bouton bloqué.\
Si un mod lève cette restriction : alors le script lui permettra de repousser les morts-vivants.\
À l'inverse si un mod ajoute un autre kit qui ne peut repousser les morts-vivants, alors le script ne lui permettra pas de les repousser.
````

````{tab-item} À portée
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 4)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    !ModalState(TURNUNDEAD)
    !CheckStat(Myself, 0, TURNUNDEADLEVEL)
    See(NearestEnemyOfType([0.UNDEAD]))
    Range(LastSeenBy(Myself), 13)
THEN
    RESPONSE #1
        Turn()
END
```
Le facteur environnemental est important.\
Repousser les morts-vivants est rarement utile en plein centre-ville.\
La condition la plus simpliste est donc de vérifier la présence de morts-vivants ennemis.\
Comme la capacité de repoussement a une portée de 13, on rajoute cette contrainte.

À savoir que dans ce cas, les personnages `NEUTRAL` ne repousseront pas les morts-vivants.\
Il est à noter que des morts-vivants charmés par des personnages `NEUTRAL` deviennent `ENEMY`.
````

````{tab-item} Can Turn
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 4)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    !ModalState(TURNUNDEAD)
    !CheckStat(Myself, 0, TURNUNDEADLEVEL)
    See(NearestEnemyOfType([0.UNDEAD]))
    Range(LastSeenBy(Myself), 13)
    CanTurn(LastSeenBy(Myself), 3)
THEN
    RESPONSE #1
        Turn()
END
```
Le fait d'être à portée n'est pas suffisant en soi.\
L'adversaire peut-être immunisé aux effets ou posséder un niveau trop élevé par rapport au niveau de repoussement de la créature active.\
C'est ici que `CanTurn` rentre en action.\
Cette action va s'assurer que le personnage a une chance de repousser la cible.\
Des informations complémentaires sont présentées ci-dessous pour vous permettre de choisir une valeur cohérente à vos besoins.
````

````{tab-item} Élitéiste
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 4)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    !ModalState(TURNUNDEAD)
    !CheckStat(Myself, 0, TURNUNDEADLEVEL)
    See(NearestEnemyOfType([0.UNDEAD]))
    Range(LastSeenBy(Myself), 13)
    CanTurn(LastSeenBy(Myself), 8)
THEN
    RESPONSE #1
        Turn()
END
```
Même principe que le précédent, mais le personnage s'assure du résultat maximal avec la probabilité maximale.
````

````{tab-item} Can Turn groupé
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 4)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    !ModalState(TURNUNDEAD)
    !CheckStat(Myself, 0, TURNUNDEADLEVEL)
    See(ThirdNearestEnemyOfType([0.UNDEAD]))
    Range(LastSeenBy(Myself), 13)
    OR(3)
        CanTurn(LastSeenBy(Myself), 0)
        CanTurn(SecondNearestEnemyOfType([0.UNDEAD]), 0)
        CanTurn(NearestEnemyOfType([0.UNDEAD]), 0)
THEN
    RESPONSE #1
        Turn()
END
```
Face à un groupe de morts-vivants, on peut abaisser le niveau de différence minimal pour activer le repoussement.\
Lorsque l'on tombe sur un groupe, il n'est pas rare que la plupart des ennemis soient de niveau similaire.
````
`````



## CanTurn: La différence de niveau qui va bien

En utilisant l'action `CanTurn`, il est nécessaire d'y associer une valeur. Laquelle choisir ?\
La valeur de la différence de niveau est délicate et propre à chacun. Mais quelques informations permettront de faire des choix pertinents.

Voici un tableau récapitulatif :\
**Différence de niveau** = Niveau de repoussement du repousseur - Niveau d'expérience du repoussé

| Différence de niveau | % de repoussement mineur | % de repoussement majeur |
| -- | -- | -- |
| -1 | 0% | 0% |
| 0 | 25% | 0% |
| 1 | 50% | 0% |
| 2 | 75% | 0% |
| 3 | 100% | 0% |
| 4 | 100% | 0% |
| 5 | 75% | 25% |
| 6 | 50% | 50% |
| 7 | 25% | 75% |
| 8 | 0% | 100% |

On distingue deux bornes :
* En dessous de 0, l'efficacité est nulle
* À partir de 8, l'efficacité est maximale

On a deux bornes intermédiaires :
* À partir de 3, on assure un effet (mineur ou majeur)
* À partir de 5, on a la possibilité de réussir un repoussement majeur