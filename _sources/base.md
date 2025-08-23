# Introduction


## Deux besoins à respecter

Les *Actions* ont leurs propres contraintes :
1. Les contraintes que le moddeur juge pertinentes pour la réalisation de l'action
2. Les contraintes inhérentes aux actions du bloc `RESPONSE`


Un exemple connu est celui de l'initialisation d'un dialogue.\
Si je fais :
```cr
IF
    See(Player1)
    Global("myvar","LOCALS",1)
THEN
    RESPONSE #100
        StartDialogNotSet(Player1)
        SetGlobal("myvar","LOCALS",2)
END
```


Pas mal de moddeurs risquent de sauter au plafond.

Car ce n'est pas parce que l'action `StartDialogNotSet` est appelée qu'elle sera réellement appliquée.\
Les contraintes internes à `StartDialogNotSet` doivent également être respectées.

*Exemple* : si le personnage est endormi, `StartDialogNotSet` ne fonctionnera pas.

Or, cette contrainte n'est **pas** vérifiée dans le `IF`.\
Le bloc s'éxécutera mais pas en totalité :
* `SetGlobal` oui
* `StartDialogNotSet` non

Or si ce cas se produit, nous nous retrouvons avec un mod buggué, car le dialogue ne pourra jamais redémarrer : `GetGlobal("myvar","LOCALS")` valant 2.

Chaque Action aura ses spécificités mais il y a un aspect redondant à prendre en compte : la présence (ou non) de l'action dans les fichiers IDS : `ACTION`, `ACTSLEEP` et `INSTANT`.


## Trois types d'Action

TODO

