# Introduction

Avant même de coder un script, il est important de savoir les contraintes qui leurs sont associés.\
Certains états du personnage influent lourdement (et implicitement) l'exécution des actions ou même des scripts dans leur intégralité.

## Mon script est-il actif ?


La caractéristique `SUMMON_DISABLE_ACTION` bloque les scripts.

C'est aussi le cas de certains états de Charme, de Confusion et de Débilité mentale.\
Dans ces cas, le personnage applique un script codé en dur.

D'autres cas bloquent également le cycle mais l'information n'est pas "captable" par les scripts.

Concrètement, cela signifie que des vérifications sur Myself comme `StateCheck(Myself, STATE_CHARMED | STATE_CONFUSED | STATE_FEEBLEMINDED)` ou `!CheckStat(Myself, 0, SUMMON_DISABLE_ACTION)` n'ont pas de sens car comme les scripts sont inactifs, elles ne peuvent valoir TRUE.



## Les contraintes des Actions

Les *Actions* ont leurs propres contraintes :
1. Les triggers que le moddeur juge pertinents pour la réalisation de l'action
2. Les contraintes inhérentes aux actions du bloc `RESPONSE`


Un exemple connu est celui de l'initialisation d'un dialogue :
```cr
IF
    See(Player1)
    Global("myvar","LOCALS",1)
THEN
    RESPONSE #100
        SetGlobal("myvar","LOCALS",2)
        StartDialogNotSet(Player1)
END
```

Ce n'est pas parce que l'action `StartDialogNotSet` est appelée qu'elle sera réellement appliquée.\
Les contraintes internes à `StartDialogNotSet` doivent également être respectées.

*Exemple* : si le personnage est endormi, `StartDialogNotSet` ne fonctionnera pas.

Or, cette contrainte n'est **pas** vérifiée dans le `IF`.\
Le bloc s'éxécutera mais pas en totalité :
* `SetGlobal` oui
* `StartDialogNotSet` non

Or si ce cas se produit, nous nous retrouvons avec un mod buggué, car le dialogue ne pourra jamais redémarrer : `Global("myvar","LOCALS")` valant 2.

Chaque Action aura ses spécificités mais il y a un aspect redondant à prendre en compte : la présence (ou non) de l'action dans les fichiers IDS : `ACTION`, `ACTSLEEP` et `INSTANT`.


## Trois types d'Action

Les actions sont réparties dans 3 fichiers IDS distincts.\
Chacun d'entre eux ont des contraintes différentes pour être exécuter.


### ACTION

Les actions présentes dans le fichier ACTION et absentes de INSTANT et ACTSLEEP imposent deux conditions :
- `!StateCheck(Myself, STATE_HELPLESS | STATE_SLEEPING | STATE_DEAD)`
- `!CheckStat(Myself, 0, CASTERHOLD)`



### ACTSLEEP

Les actions présentes dans le fichier ACTSLEEP et absentes de INSTANT imposent deux conditions :
- `!StateCheck(Myself, STATE_HELPLESS)`
- `!CheckStat(Myself, 0, CASTERHOLD)`

Prenons l'exemple de l'action `Dialog`, qui est dans ACTSLEEP sans être dans INSTANT.\
Si Myself a le STATE_HELPLESS actif, alors l'action ne s'exécutera pas. Quelques soient les triggers renseignés.\
Ainsi, pour éviter les problèmatiques, mieux vaut rajouter ces conditions nous-mêmes.


### INSTANT

Les actions du fichier INSTANT.IDS s'exécutent sans surcharger la pile des actions et donc n'ont pas de contraintes spécifiques hormis celles qui bloqueraient l'exécution des scripts et ne nécessitent donc pas de traitement particulier.
