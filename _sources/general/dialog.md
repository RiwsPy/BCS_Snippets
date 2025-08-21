# Dialogue - Initialisation

## Objectif
Initier un dialogue entre deux personnages.

----- 

Ex Bête noire des moddeurs, véritable générateur de bug et grand maître arracheur de cheveux, la question : « *À quel moment je peux être sûr de pouvoir dialoguer avec un personnage* » a dû faire perdre quelques heures de sommeil à pas mal de personne.

Mais ça, c'était avant...\
Il existe désormais un template presque automatique à appliquer partout. Tout le temps et c'est comme ça et puis c'est tout.


## Les deux enjeux

1. **Technique** : être certain que le dialogue va pouvoir se lancer
1. **RP** : qu'il se lance au moment opportun (hors combat, en capacité de parler etc)


------

`````{tab-set}
````{tab-item} BCS Snippet
```cr
IF
    ActionListEmpty()
    !ActualityInCombat()
    !StateCheck(Myself, CD_STATE_NOTVALID)
    !StateCheck(Player1, CD_STATE_NOTVALID)
    Allegiance(Myself, PC)
    !ButtonDisabled(BUTTON_DIALOG)
    NextTriggerObject(Player1)
    !ButtonDisabled(BUTTON_DIALOG)
    !Detect([EVILCUTOFF])
    See(Player1)
THEN
    RESPONSE #100
        StartDialogueNoSet(Player1)
END
```
````
````{tab-item} Classique
```cr
IF
    CombatCounter(0)
    InParty(Myself)
    !StateCheck(Player1,CD_STATE_NOTVALID)
    !StateCheck(Myself,CD_STATE_NOTVALID)
    !See([ENEMY])
    See(Player1)
THEN
    RESPONSE #100
        StartDialogueNoSet(Player1)
END
```
````
`````


On vérifie que :
- Il n'y a pas de combat en cours
- Les deux créatures sont en état de parler
- Elles se voient
- La créature active fait partie du groupe (dans le cas d'un banter)



## Analyse des blocs

### CombatCounter(0)

Pratique pour savoir si aucun combat n'est en cours mais il est un peu buggé : il renvoie faux à la fin d'un dialogue par exemple.\
Dans ce cas précis c'est secondaire mais mieux vaut prendre les bonnes hatitudes dès le début.

```{tip}
`CombatCounter(0)` → `!ActualityInCombat()` ou `CombatCounterLT(1)`
```


### InParty(Myself)

C'est un classique pour vérifier que le personnage fait bien partie du groupe avant de lancer un dialogue.\
Est-ce une nécessité ? Pas forcément.\
De nos jours, il y a des mods de "suiveurs" qui permettent d'avoir des « 7ème » membre du groupe.\
On pourrait vouloir que ces personnages puissent discuter avec le groupe sans en faire partie.\
Ces suiveurs ont une EA qui vaut `FAMILIAR`.

````{admonition} Alternatives
:class: dropdown, tip
```cr
    Allegiance(Myself, GOODCUTOFF)
```

Ou un peu plus restrictif :
```cr
    OR(2)
        InParty(Myself)
        Allegiance(Myself, FAMILIAR)
```

Ou encore un peu plus restrictif :
```cr
    OR(2)
        Allegiance(Myself, PC)
        Allegiance(Myself, FAMILIAR)
```

```{admonition} Pourquoi Allegiance(Myself, PC) est-il plus restrictif que InParty(Myself) ?
:class: note, dropdown
Être membre du groupe ne renseigne pas sur l'allégeance du personnage. Un membre du groupe `NEUTRAL` ou charmé par un adversaire renvoie faux dans le premier cas et vrai dans le second.
```
````

````{tip}
`InParty(Myself)` → `Allegiance(Myself, PC)`\
ou, s'il on veut pouvoir discuter avec les suiveurs
```cr
OR(2)
    Allegiance(Myself, PC)
    Allegiance(Myself, FAMILIAR)
```
````


### See([ENEMY])

Un classique pour déterminer si des adversaires sont à proximité.
```{admonition} N'est-il pas redondant avec CombatCounter(0) ?
:class: dropdown
Il arrive que des adversaires soient visibles sans qu'aucun combat ne soit en cours. C'est donc un complément intéressant.
```

Le problème de `ENEMY` c'est qu'il ne regroupe pas tous les personnages adverses.\
`EVILCUTOFF` ici est plus large car il concerne toutes les créatures avec un cercle de sélection rouge.\
C'est notamment pertinent pour détecter les membres du groupe charmés, nous y reviendrons plus bas.

La plupart du temps, le dialogue n'est pas urgent et on ne veut pas déclencher un dialogue s'il y a des ennemis à proximité même si aucun combat n'a lieu.\
Or `See` a des limites que ne possède pas `Detect` dans sa capacité à détecter les créatures invisibles.

```{tip}
`See([ENEMY])` → `Detect([EVILCUTOFF])`
```

## StartDialogueNoSet

Pour savoir si la solution proposée est cohérente, il faut connaître `StartDialogueNoSet` et vérifier que ses contraintes sont bien respectées dans le `IF`.\
On explique pourquoi [ici](/base).

Les contraintes du `StartDialogueNoSet` s'applique sur la personne qui va initier le dialogue. On va donc se focaliser sur elle.

## Les manques

### STATE_CHARMED

L'ajout d'une condition pourrait être intéressante dans les versions originales du snippet car `STATE_CHARMED` ne matche pas avec `CD_STATE_NOTVALID`.\
`Player1` pourrait être charmé avec un EA à `CHARMED_PC` (qui ne matche pas avec `[ENEMY]`).

Mais dans notre cas, avec l'utilisation de `!Detect([EVILCUTOFF])` et `Allegiance(Myself, PC)`, on gère déjà bien assez ce cas de figure.

### Arrêt du temps en cours

> Vous connaissez l'histoire du mage qui arrête le temps et qui est soudain pris d'un doute ? Il pose alors une question à son pote…

Il n'existe pas de moyen sûr pour déterminer si un arrêt du temps est en cours ou non.\
Et comme il ne s'agit pas d'un sort offensif, le compteur de combat n'est d'aucune utilité.\
Il y a une solution alternative, incomplète, qui offre des effets secondaires intéressants :
```
    !ButtonDisabled(BUTTON_DIALOG)
```
Le bouton de dialogue est désactivé lors d'un Arrêt du temps.\
De plus, ce bouton peut être désactivé pour d'autres raisons : Illusion projetée, plusieurs polymorphies…\
Autant de raisons RP où le dialogue ne devrait pas avoir lieu.

````{admonition} Ajout de
:class: tip
```cr
!ButtonDisabled(BUTTON_DIALOG)
NextTriggerObject(Player1)
!ButtonDisabled(BUTTON_DIALOG)
```
````


### HELD / CASTERHOLD / SUMMON_DISABLE_ACTION / Insélectionnable / IsActive

TODO. Trop marginales pour être pertinente (dans la version vanilla).



## Pour aller plus loin

### Polymorphie

Certaines polymorphies ne bloquent pas le bouton dialogue, dans ce cas, il est possible de forcer cette condition :
```cr
CheckStat(Myself, 0, POLYMORPHED)
CheckStat(Player1, 0, POLYMORPHED)
```

### Surdité

On gère le cas du silence mais pas celui de la surdité. Les dialogues étant verbaux, ajouter une contrainte peut être pertinente pour des raisons RP. Libre à vous de le rajouter ou non.\
C'est d'autant plus intéressant qu'avec `StartDialogueNoSet` les personnages peuvent se parler de loin.

```cr
!CheckSpellState(Myself, DEAFENED)
!CheckSpellState(Player1, DEAFENED)
```

Ou moins restrictif, si on considère qu'ils sont malentendants.

```cr
OR(2)
    !CheckSpellState(Myself, DEAFENED)
    Range(Player1, 10)
OR(2)
    !CheckSpellState(Player1, DEAFENED)
    Range(Player1, 10)
```

## Performances

Ici, les actions qui demanderont des ressources pour les calculs sont `See` et `Detect`.\
Il est donc conseillé de les placer à la fin du bloc.



## Bonnes pratiques

Ce que l'on comprend c'est qu'il y a pas de façon unique ou même complète pour régler ce genre de problèmatique. Cela dépend notamment du contexte et de la conception du moddeur.\
Comme on ne peut pas être assuré que le dialogue se lance, une règle s'impose : ne pas setter de variables dans le script en même temps que le lancement du dialogue.

Voilà ce qu'il ne faut **pas** faire :
````{warning}
```cr
IF
    Global("NOT_TODO", "LOCALS", 0)
    //# …
THEN
    RESPONSE #100
        StartDialogueNoSet(Player1)
        SetGlobal("NOT_TODO", "LOCALS", 1)
END
```
````

Car si le dialogue ne se lance pas pour une condition ou une autre, la variable est modifiée et le dialogue ne se lancera donc **jamais**.
S'il est important de modifier cette variable, une solution s'offre à vous :

Insérez le contenu du block `RESPONSE` dans le dialogue. C'est le seul moyen d'être sûr qu'il soit bien lancé.
````{tip}

```cr
IF
    Global("OK", "LOCALS", 0)
    //# …
THEN
    RESPONSE #100
        StartDialogueNoSet(Player1) //# mettre le SetGlobal dans le dialogue (ou tout autre évènement qui va empêcher le bloc de se répéter)
END
```
````
