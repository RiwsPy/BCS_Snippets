---
myst:
  substitutions:
    OBJ_FILENAME: ITEM_FILENAME
---

# Can I Use a Item?

Il y a deux enjeux :
1. Savoir si l'objet est utilisable par le personnage
1. Savoir si le joueur souhaite que le personnage puisse utiliser l'objet

Une question simple permet de répondre à ces deux questions de manière efficace : _l'objet est-il équipé ?_

Si oui, on considère que :
- il est "normalement" utilisable par le personnage (puisqu'il a pu s'en équiper)
- le joueur ou le moddeur autorise son utilisation (sinon il fallait le laisser dans le sac)

Cela permet ainsi d'assurer au joueur de contraindre facilement le script :
- Quelle potion/munition/arme/... sont autorisées (celles équipées)
- Lesquelles ne sont pas autorisées (celles dans le sac)
- Combien il peut en utiliser
- Ajouter/enlever des objets pendant le combat

Cerise sur le gâteau, cela permet aussi d'améliorer les performances du script en limitant le nombre de slots à checker.


## Snippets de base

Il s'agit d'une base qui peut/doit être adapté aux besoins.

`````{tab-set}
````{tab-item} Basique
```cr
IF
    ActionListEmpty()
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    Global("BDAI_DISABLE_ITEMS", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    HasItemEquiped("ITEM_FILENAME", Myself)

    //# TODO: Code spécifique
THEN
    REPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("ITEM_FILENAME", Myself)
END
```
On vérifie que :
- un round s'est déroulé depuis la dernière action
- le personnage puisse utiliser des objets
- l'object est équipé
- l'action UseItem puisse s'exécuter
- le personnage n'est pas en train de réaliser une action
````

````{tab-item} Objets rapides
```cr
IF
    ActionListEmpty()
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    Global("BDAI_DISABLE_ITEMS", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    HasItemEquiped("ITEM_FILENAME", Myself)
    OR(3)
        !ButtonDisabled(BUTTON_QUICKITEM1)
        !ButtonDisabled(BUTTON_QUICKITEM2)
        !ButtonDisabled(BUTTON_QUICKITEM3)

    //# TODO: Code spécifique
THEN
    REPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("ITEM_FILENAME", Myself)
END
```
On vérifie que :
- Au moins un des BUTTON_QUICKITEM est débloqué (cas du double illusoire par exemple)
````

````{tab-item} Capacités d'équipement
```cr
IF
    ActionListEmpty()
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    Global("BDAI_DISABLE_ITEMS", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    HasItemEquiped("ITEM_FILENAME", Myself)
    !ButtonDisabled(BUTTON_USEITEM)

    //# TODO: Code spécifique
THEN
    REPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("ITEM_FILENAME", Myself)
END
```
On vérifie que :
- Le bouton des capacités d'équipement est actif
````
`````


## Autre

### Enchaînement

Quand Enchaînement est en cours, privilégiez l'utilisation des sorts...

```cr    
    CheckStat(Myself, 0, AURACLEANSING)
```
