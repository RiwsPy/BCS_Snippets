# Résurrection de masse (SPPR729)

Ce sort est un peu particulier car il est efficace sur tous les membres du groupe.\
Il soigne également de 3D10 points de vie. Mais soyons honnête : ça serait du gachis de l'utiliser que pour ça.

Une difficulté ici est que l'on ne peut pas se baser sur des [object], car les personnages sont morts.

`````{tab-set}
````{tab-item} BCS Snippet
```cr
IF
    //# …
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
```
Il est possible de connaître la taille du groupe ainsi que le nombre de membre encore en vie.\
Et sans pouvoir faire de soustraction, il est possible de faire des comparaisons.

Le sort est lancé si :
- il y a au moins 3 morts dans le groupe
- ou s'il n'y a qu'un survivant dans un groupe de 3
- ça gère même certain cas où il y aurait plus de 6 personnes dans le groupe
````
`````


````{note}
N'hésitez pas à attendre la fin du combat et de le faire lancer que par les alliés.

```cr
IF
    !ActuallyInCombat()
    Allegiance(Myself, GOODCUTOFF)
    Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0)
    !See(NearestEnemyOf(Myself))
    //# …
THEN
```
````