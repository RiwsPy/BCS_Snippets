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
- Il est "normalement" utilisable par le personnage (puisqu'il a pu s'en équiper)
- Le joueur ou le moddeur autorise son utilisation (sinon il fallait le laisser dans le sac)

Cela permet ainsi d'assurer au joueur de contraindre facilement le script :
- Quelle potion/munition/arme/... sont autorisées ? Celles équipées
- Lesquelles ne sont pas autorisées ? Celles dans le sac
- Combien peut-il en utiliser ? Le nombre d'équipés
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
- l'objet est équipé
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



## Trigger UseItem

Il est intéressant - et courant dans les scripts - de pouvoir utiliser un objet spécifique même dans le sac.

Avant cela, il faut posséder l'objet.


## HasItem et NumItems

Deux triggers existent pour déterminer si un personnage possède un objet spécifique.

| Effet | HasItem | NumItems |
| - | - | - |
| S'arrête dès que trouvé | ✅ | ❌ |
| Check le sac à dos | ✅ | ✅ |
| Check les sacs de contenance | ✅ | ❌ |

`HasItem` est généralement utilisé avant l'utilisation des objets. Il est potentiellement plus rapide.\
`NumItems` est globalement inutilisé.

Or, il y a un problème :\
`UseItem` ne peut cibler les objets dans les sacs de contenance.\
Et `HasItem` renvoie vrai si l'objet est présent dans un sac de contenance.\
Il peut donc arriver que les triggers soient vrais mais que l'action soit impossible.\
Il est donc conseillé de privilégier `NumItemsGT("ITEM_FILENAME", Myself, 0)`, notamment pour les membres du groupe.



### Note
Si la créature ne possède pas l'objet, il est possible de le créer avant avec `GiveItemCreate("ITEM_FILENAME", Myself, 1, 1, 1)`.\
Attention tout de même :
- Pour les membres du groupe, cela affiche une StrRef
- Si l'inventaire est plein, l'objet dans le premier slot du sac est abandonné

Il convient donc de le privilégier pour les non-membres du groupe.


## Gestion des objets rechargeables

`HasItem` et `NumItems` sont pratiques, notamment pour les objets à charge unique comme les potions.\
Mais il arrive que des objets possèdent plusieurs charges et que le fait de posséder l'objet ne suffise pas à savoir s'il est utilisable ou non. Cela arrive fréquemment avec les objets équipés.

Malheureusement, il n'existe pas de solution pour connaître le nombre de charges restantes d'un objet.\
Les moddeurs procèdent alors à l'inverse : ils comptent le nombre de charge utilisée.\
Puis, après un repos du groupe, les compteurs sont remis à 0.

```cr
IF
    PartyRested()
THEN
    RESPONSE #100
        SetGlobal("ITEM_FILENAME", "GLOBAL", 0)
END

IF
    //# …
    GlobalLT("ITEM_FILENAME", "GLOBAL", 2)
THEN
    RESPONSE #100
        UseItem("ITEM_FILENAME", Myself)
        IncrementGlobal("ITEM_FILENAME", "GLOBAL", 1)
END
```

Cela a plusieurs limites importantes :
1. L'utilisation de plusieurs objets de même nom vont entrer en conflit
1. Le nombre de charge doit être défini et connu en amont (ici, 2)
1. Si l'utilisation de l'objet a échoué, la variable est tout de même incrémentée (suite à l'utilisation maladroite de `HasItem` par exemple)
1. L'utilisation manuelle des objets par le joueur ou un autre script n'est pas prise en compte
1. Après une action `Rest`, les variables ne sont pas remises à 0
1. Chaque objet nécessite sa propre variable

### Alternatives

On se rend vite compte que ce n'est pas parfait en l'état. Il convient donc de chercher des alternatives.

#### GLOBAL ou LOCALS

`GLOBAL` a l'avantage de fonctionner pour l'intégralité du groupe, par exemple si deux personnages différents utilisent chacun une charge d'un même objet.\
`LOCALS` permet d'individualiser l'utilisation, ainsi un `ENEMY` pourra utiliser l'objet sans bloquer son utilisation par les membres du groupe.

La solution va dépendre du contexte.\
L'utilisation de `LOCALS` nous paraît plus générique et c'est la solution qui a été choisie ici mais ce n'est pas une obligation.


#### WeiDU

On pourrait incrémenter les variables dans l'objet même.\
Cela permettrait de comptabiliser que les utilisations réelles de l'objet (3.), et cela fonctionnerait même si c'est le joueur qui utilise l'objet manuellement (4.).\
Cela impliquerait de remplacer les `GLOBAL` par des `LOCALS`.

Problème : si l'objet n'a plus de charge, la variable ne sera jamais incrémentée et le personnage essayera toujours d'utiliser l'objet.



#### Unusable, le blocage par l'apprentissage

Le trigger `Unusable` nous apporte une information utile ici.\
Une fois l'objet déchargé, ce trigger vaut vrai lors du cycle de script suivant. Cela est intéressant si couplé avec le timer `BD_Cast`.

```cr
IF
    PartyRested()
THEN
    RESPONSE #100
        SetGlobal("ITEM_FILENAME_IS_EMPTY", "LOCALS", 0)
END

IF
    Unusable("ITEM_FILENAME")
THEN
    RESPONSE #100
        SetGlobal("ITEM_FILENAME_IS_EMPTY", "LOCALS", 1)
        Continue()
END

IF
    //# …
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    Global("ITEM_FILENAME_IS_EMPTY", "LOCALS", 0)
THEN
    RESPONSE #100
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("ITEM_FILENAME", Myself)
END

```

Cela permet de bloquer l'utilisation d'un objet sans avoir à connaître son nombre de charge (2.).\
Que l'utilisation de l'objet échoue ou non importe peu (3.).\
Au pire des cas, le personnage essayera qu'une seule fois d'utiliser l'objet avant de le bloquer (4.).\
La problématique de l'action `Rest` est réduite aux objets vidés (dont la variable `IS_EMPTY` = 1) (5.).


Problème : cela ne permet pas de gérer efficacement les objets qui auraient plusieurs capacités (dès qu'une capacité est vidée, l'objet est bloqué).


#### BitSet / BitCheck (optionnel)

Grâce au trigger `Unusable`, on ne sauvegarde qu'une valeur binaire (0 ou 1) dans les variables plutôt qu'un nombre d'utilisation.\
Il devient alors possible d'utiliser une seule variable avec l'action `BitSet`. Chaque bit correspond à un objet, on peut donc sauvegarder jusqu'à 32 variables en une seule.\
Lors du `PartyRested()`, une seule variable est à reset. Cela permet de le rajouter simplement à d'autres scripts/objets.

Exemple avec deux objets :
```cr
IF
    PartyRested()
    !Global("EMPTY_ITEMS", "LOCALS", 0)
THEN
    RESPONSE #100
        SetGlobal("EMPTY_ITEMS", "LOCALS", 0) //# Une seule variable est à reset
END

IF
    Unusable("ITEM_FILENAME1")
THEN
    RESPONSE #100
        BitSet("EMPTY_ITEMS", "LOCALS", BIT1) //# Chaque bit correspond à un objet
        Continue()
END

IF
    Unusable("ITEM_FILENAME2")
THEN
    RESPONSE #100
        BitSet("EMPTY_ITEMS", "LOCALS", BIT2)
        Continue()
END

IF
    //# …
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    !BitCheck("EMPTY_ITEMS", "LOCALS", BIT1)
THEN
    RESPONSE #100
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("ITEM_FILENAME1", Myself)
END

IF
    //# …
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    !BitCheck("EMPTY_ITEMS", "LOCALS", BIT2)
THEN
    RESPONSE #100
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("ITEM_FILENAME2", Myself)
END
```


## Autre

### Enchaînement

Quand Enchaînement est en cours, privilégiez l'utilisation des sorts... en bloquant par défaut l'utilisation des objets.

```cr    
    CheckStat(Myself, 0, AURACLEANSING)
```
