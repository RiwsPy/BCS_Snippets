---
myst:
  substitutions:
    OBJ_FILENAME: SPELL_FILENAME
    SPELL: SPELL_NAME
    SPELL_TYPE: DEFENSIVE_OR_OFFENSIVE_TODO
    TARGET: TARGET
---

# Can I Cast a Spell?

Avant de lancer des sorts, il est utile de savoir si celui-ci a des chances d'être incanté.\
Plusieurs paramètres sont à prendre en compte.

## Snippets de base

Adaptés pour les actions `Spell` et `SpellRES`.\
Il s'agit d'une base qui peut/doit être adapté aux besoins.

`````{tab-set}
````{tab-item} Basique
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_DEFENSIVE_OR_OFFENSIVE_TODO", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    HaveSpell(SPELL_NAME)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(TARGET, SPELL_NAME)
END
```
````

````{tab-item} Sort profane
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_NO_ARCANE", "LOCALS", 0)
    Global("BDAI_DISABLE_DEFENSIVE_OR_OFFENSIVE_TODO", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    HaveSpell(SPELL_NAME)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(TARGET, SPELL_NAME)
END
```
````

````{tab-item} Sort divin/druidique
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    Global("BDAI_DISABLE_DEFENSIVE_OR_OFFENSIVE_TODO", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELLFAILUREPRIEST)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    HaveSpell(SPELL_NAME)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(TARGET, SPELL_NAME)
END
```
````

````{tab-item} Capacités spéciales
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_INNATEBUTTON)
    Global("BDAI_DISABLE_DEFENSIVE_OR_OFFENSIVE_TODO", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    CheckStatLT(Myself, 50, SPELL_FAILURE_INNATE)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    HaveSpell(SPELL_NAME)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        Spell(TARGET, SPELL_NAME)
END
```
````

````{tab-item} Parchemins
```cr
IF
    ActionListEmpty()
    !GlobalTimerNotExpired("BD_Cast", "LOCALS")
    Global("BDAI_DISABLE_ITEMS", "LOCALS", 0)
    !StateCheck(Myself, STATE_SLEEPING | STATE_HELPLESS | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    HasItemEquiped("SPELL_FILENAME", Myself)
    Global("BDAI_NO_ARCANE", "LOCALS", 0) //# Uniquement pour les parchemins profane
    Global("BDAI_DISABLE_DEFENSIVE_OR_OFFENSIVE_TODO", "LOCALS", 0)
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    OR(3)
        !ButtonDisabled(BUTTON_QUICKITEM1)
        !ButtonDisabled(BUTTON_QUICKITEM2)
        !ButtonDisabled(BUTTON_QUICKITEM3)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        UseItem("SPELL_FILENAME", TARGET)
END
```

Les parchemins étant des objets hybride, leur fonctionnement diffère sur plusieurs points.
````
`````

## Posséder le sort

```{note}
Actions concernées :
- `Spell`
```

Dans la plupart des cas, on ne voudra lancer que les sorts que l'on possède, on utilisera alors le trigger `HaveSpell` ou `HaveSpellRES` avec l'action `Spell`.\
Si on souhaite lancer des sorts non possédés on privilégiera les actions `SpellNoDec`, `ForceSpell` ou `ReallyForceSpell` ou encore `ForceSpellDead`.\
Le choix de l'action impacte fortement le choix des contraintes du block `IF`.

### Snippet

```cr
    HaveSpell(SPELL_NAME)
```

(silence-section)=
## Silence

```{note}
Actions concernées :
- `Spell`
```

Il existe une condition minimale à l'incantation des sorts : ne pas être silencieux.\
Pour cela, nous avons `StateCheck(Myself, STATE_SILENCED)`.\
C'est une chose que vous verrez dans tous les scripts depuis 25 ans.

Mais il faut bien penser que nombre de capacités et quelques sorts peuvent être lancés sous silence.\
Il faut donc bien penser à le mettre que lorsque cela est bien nécessaire.

Deux inconvénients :
1. les fautes d'inattention sont fréquentes
1. des mods peuvent modifier la composante verbale des sorts, le script devient alors partiellement inadapté

---- 

Il existe une solution à ces deux problèmatiques, que l'on utilise déjà : `HaveSpell`.\
Celui-ci vérifie que :
- le sort est mémorisé
- si le sort nécessite une composante verbale, le lanceur de sort n'est pas sous l'effet d'un silence
- le lanceur n'est pas sous l'effet empêchant le lancement de sort (comme celle dans la `Transformation de Tenser` par exemple)

Cela signifie que la présence d'une composante verbale est checkée au moment de le lancer :
- gestion dynamique des modifications des moddeurs
- plus d'erreur d'inattention


## Blocage de la capacité à lancer des sorts

```{note}
Actions concernées :
- `Spell`
```

Infligé par l'[Opcode 145](https://gibberlings3.github.io/iesdp/opcodes/bgee.htm#op145), cet effet est géré automatiquement par `HaveSpell`.\
Voir le point sur [Silence](silence-section) pour plus d'informations.

## Poison

```{note}
Actions concernées :
- `Spell`
- `SpellNoDec`
```

Un désastre pour les magiciens, le poison lui inflige des dégâts sur la durée. Double effet kisscool :
1. Il n'a pas beaucoup de points de vie donc c'est vite dramatique
2. Cela affecte clairement sa capacité à lancer des sorts (et de façon imprévisible)

Le comble étant qu'il existe des sorts pour se soigner mais quelle est leur probabilité de réussite ?

### État des lieux des autres mods

* ep scripts : un mage empoisonné ne lance plus aucun sort
* bddefai : yolo on tente quand même
* uScript v2 : yolo sauf pour Contagion


La raison pour laquelle les moddeurs y répondent par des solutions opposées c'est qu'il n'y a pas de réponse.\
Mais tentons une analyse.


### TheoryCrafting sur le poison


Le poison frappe avec une fréquence fixe pouvant provoquer aléatoirement à chaque fois une interruption de l'incantation du sort.

Lancer un sort est donc risqué car la probabilité d'échec est réelle mais… est-ce moins risqué de ne lancer AUCUN sort pendant 30 secondes ?

Idéalement il faudrait incanter le sort entre deux dégâts afin de ne pas être inquiété. Sauf que l'on ne connait ni sa fréquence ni le moment où il va frapper.


Voici quelques chiffres approximatifs :

```{note}
- Répartition des poison en fonction de leur fréquence des dégâts :
  * 1 seconde : 75%
  * 2 secondes : 5%
  * 3 secondes : 20%

- Temps nécessaire à l'incantation des sorts : valeur × 0.6 secondes

- Probabilité d'échec si des dégâts du poison sont infligés lors de l'incantation : 50%
```

Pourcentage d'échec d'incantation approximatif en fonction de la durée d'incantation :
| Durée d'incantation | % d'interruption | Risque
| ---- | --------- | ----- |
| 1 | 25% | Acceptable |
| 2 à 4 | 43% à 48% | Déconseillé |
| 5 et + | 50% | Pas envisagé |


### Balance ton sort (de niveau 1)

Les sorts de niveau faible ont deux avantages :
1. Ils ont généralement une durée d'incantation courte, donc un taux de réussite plus important
2. En cas d'échec, le sort perdu est beaucoup moins dramatique

Quelques exceptions existent, comme les mots de pouvoir qui ont un temps d'incantation très court.\
Il peut donc être intéressant de s'autoriser de lancer des sorts à incantation très rapide même empoisonné.

Note : les parchemins ne peuvent être interrompus par le poison.


### Snippet
Pour s'assurer qu'un personnage n'est pas empoisonné ou qu'il ne puisse en subir les dégâts :
```cr
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
```

Il est possible d'être immunisé aux dégâts du poison sans être immunisé au poison en tant qu'effet (et inversement).

## Maladie

```{note}
Actions concernées :
- `Spell`
- `SpellNoDec`
```

Côté maladie, on observe une fréquence plus faible.

```{note}
Répartition des poison en fonction de leur fréquence d'apparition :
* 1 seconde : 10%
* 2 secondes : 10%
* 6 secondes : 80%
```

De plus, les effets de la maladie sont plus variés (toutes n'infligent pas des dégâts).\
Et il est difficilement possible de pouvoir les différencier.\
Il serait alors dommage d'empêcher un mage de lancer des sorts parce qu'il a une maladie qui lui donne un malus de 1 en Force.\
Il serait quand même envisageable de bloquer les sorts de haut niveau, au cas où.


### Snippet
Pour s'assurer qu'un personnage n'est pas malade ou qu'il ne puisse pas subir les dégâts de la maladie :
```cr
    OR(2)
        !CheckSpellState(Myself, DISEASED)
        CheckStatGT(Myself, 99, RESISTPOISON)
```
Mais sans certitude que la maladie inflige bien des dommages. Ce snippet ne sera donc pas utilisé de façon générique.


## Mages cagoulés d'Athkatla

Lire [cette page](/general/athkatla_magec).


## Hiatus entropiques

```{note}
Actions concernées :
- `Spell`
- `SpellNoDec`
- `ForceSpell`
- `ReallyForceSpellDead`
```

Les hiatus entropiques sont également à prendre en compte lors d'un lancement d'un sort.\
Il est possible de savoir si le prochain sort sera un hiatus.


### Snippet
Pour s'assurer que le prochain sort ne sera pas un hiatus :
```cr
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
```

Cela détecte également les zones d'entropies, qui n'ont pas besoin de traitement supplémentaire.

```{note}
Certains sorts ou capacités sont protégés contre les hiatus.\
Pour en [savoir plus](https://gibberlings3.github.io/iesdp/file_formats/ie_formats/spl_v1.htm#Header_Flags).
```

## Modificateur de réussite à l'incantation et zone anti-magie

3 caractéristiques influent directement sur la probabilité d'incanter les sorts :
- `SPELLFAILUREMAGE` : influe sur les sorts profanes
- `SPELLFAILUREPRIEST` : influe sur les sorts de divins et druidiques
- `SPELL_FAILURE_INNATE` : influe sur les autres sorts

Une valeur de 30 en `SPELLFAILUREMAGE` signifie que l'incantation d'un sort profane à 30% de chance d'échec.\
Ces caractéristiques sont fiables car la mécanique est gérée par le moteur du jeu.\
En se focalisant sur celles-ci, on peut ainsi traiter de nombreux cas de façon générique :

- Les zones anti-magie
- La surdité
- Les convocations d'insectes
- La capacité des tueurs de magiciens
- ...

### La "bonne" valeur ?

Voici les choix de plusieurs mods, valeur au-delà de laquelle, les sorts ne sont pas incantés :
- bddfai : 49% max
- uscript v1 : 75% max
- uscript v2 : 49% max
- ep-script : 19% max

Comme toujours, la question n'a pas une unique réponse.

En combat, par défaut, 49% paraît acceptable. Avec des variations selon le niveau du sort : 24% pour les plus hauts, 74% pour les plus bas.\
Dans toute situation où l'urgence n'existe pas (notamment hors combat), 0% d'échec est conseillé, quelque soit le sort.

La réponse peut également évoluer selon les cas de figure : un demi-dieu qui doit affronter une horde dans un donjon aura une gestion différente du minion qui sait qu'il n'a pratiquement aucune chance de survivre à l'affrontement.\
Dans une situation dramatique, 99% d'échec, soit 1% de réussite, c'est mieux que rien.

Cela peut aussi dépendre du caractère du personnage.\
Certains n'acceptant pas l'échec, d'autres aiment jouer avec le feu. Enfin, d'autres n'auront même pas conscience de tout ça et lanceront leurs sorts quelque soit la probabilité.


### Snippet

`````{tab-set}
````{tab-item} Sorts profanes
```cr
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE)
```
````
````{tab-item} Sorts divins et druidiques
```cr
    CheckStatLT(Myself, 50, SPELLFAILUREPRIEST)
```
````
````{tab-item} Autre
```cr
    CheckStatLT(Myself, 50, SPELL_FAILURE_INNATE)
```
````
`````

```{note}
Certains sorts ou capacités sont protégés contre la valeur de ces caractéristiques.\
Pour en [savoir plus](https://gibberlings3.github.io/iesdp/file_formats/ie_formats/spl_v1.htm#Header_Flags).
```

## Temps d'attente entre chaque incantation/utilisation d'objet

Concerne toutes les actions sauf `ReallyForceSpell`.
TODO


## GUI: Bouton désactivé

```{note}
Actions concernées :
- Toutes
```

On part du principe que si le bouton servant à accéder aux sorts est désactivé c'est que le personnage est en incapacité de lancer des sorts.\
C'est notamment le cas lorsque l'on porte une armure lourde ou sous polymorphie.

### Snippet

`````{tab-set}
````{tab-item} Sorts profanes, divins et druidiques
```cr
    !ButtonDisabled(BUTTON_CASTSPELL)
```
````

````{tab-item} Capacités spéciales
```cr
    !ButtonDisabled(BUTTON_INNATEBUTTON)
```
````
`````


## Spécificités

```{note}
Actions concernées :
- `Spell`
- `SpellNoDec`
```

### Can't target invisible

La plupart des sorts ciblant un personnage ne peuvent se faire si ce dernier est invisible (sauf pour soi-même).

```cr
    !StateCheck(TARGET, STATE_NOT_TARGETABLE)
    CheckStat(TARGET, 0, SANCTUARY)
```

L'action `See` filtre le plus souvent `STATE_INVISIBLE` et `SANCTUARY`.\
Dans ce cas, ce bloc suffira :

```cr
    !StateCheck(TARGET, STATE_IMPROVEDINVISIBILITY)
```

### Outdoor

Certains sorts ne fonctionnent qu'en extérieur.

```cr
    AreaType(OUTDOOR)
```

### Not in combat

Certains sorts ne fonctionnent qu'en dehors de tout combat.

```cr
    !ActuallyInCombat()
```


