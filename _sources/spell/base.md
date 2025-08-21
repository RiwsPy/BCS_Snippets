# Can I Cast a Spell?

Avant de lancer des sorts, il est utile de savoir si celui-ci a des chances d'être incanté.\
Plusieurs paramètres sont à prendre en compte.


## Snippets de base

Adaptés pour les actions `Spell` et `SpellRES`.

`````{tab-set}
````{tab-item} Basique
```cr
IF
    ActionListEmpty()
    //#Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0) //# Au choix
    //#Global("BDAI_DISABLE_OFFENSIVE", "LOCALS", 0) //# Au choix
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2) //# Potentiellement facultatif
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    //# HaveSpell(…)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        //# Spell(…, …)
END
```
````

````{tab-item} Sort profane
```cr
IF
    ActionListEmpty()
    Global("BDAI_NO_ARCANE", "LOCALS", 0)
    !ButtonDisabled(BUTTON_CASTSPELL)
    //#Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0) //# Au choix
    //#Global("BDAI_DISABLE_OFFENSIVE", "LOCALS", 0) //# Au choix
    CheckStatLT(Myself, 50, SPELLFAILUREMAGE) //# Valeur à configurer
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2) //# Potentiellement facultatif
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    //# HaveSpell(…)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        //# Spell(…, …)
END
```
````

````{tab-item} Sort divin/druidique
```cr
IF
    ActionListEmpty()
    !ButtonDisabled(BUTTON_CASTSPELL)
    //#Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0) //# Au choix
    //#Global("BDAI_DISABLE_OFFENSIVE", "LOCALS", 0) //# Au choix
    CheckStatLT(Myself, 50, SPELLFAILUREPRIEST) //# Valeur à configurer
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2) //# Potentiellement facultatif
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    //# HaveSpell(…)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        //# Spell(…, …)
END
```
````

````{tab-item} Capacités spéciales
```cr
IF
    ActionListEmpty()
    Global("BDAI_DISABLE_SPECIAL", "LOCALS", 0)
    !ButtonDisabled(BUTTON_INNATEBUTTON)
    //#Global("BDAI_DISABLE_DEFENSIVE", "LOCALS", 0) //# Au choix
    //#Global("BDAI_DISABLE_OFFENSIVE", "LOCALS", 0) //# Au choix
    CheckStatLT(Myself, 50, SPELL_FAILURE_INNATE) //# Valeur à configurer
    OR(2)
        CheckStat(Myself, 0, FORCESURGE)
        CheckStatGT(Myself, 0, CHAOS_SHIELD)
    OR(2)
        !GlobalTimerNotExpired("BD_Cast", "LOCALS")
        CheckStatGT(Myself, 0, AURACLEANSING)
    OR(2) //# Potentiellement facultatif
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
    //# HaveSpell(…)

    //# TODO: Code spécifique
THEN
    RESPONSE #1
        SetGlobalTimer("BD_Cast", "LOCALS", ONE_ROUND)
        //# Spell(…, …)
END
```
````
`````






## Posséder le sort

Dans la plupart des cas, on ne voudra lancer que les sorts que l'on possède, on utilisera alors le trigger `HaveSpell` ou `HaveSpellRES` avec l'action `Spell`.\
Si on souhaite lancer des sorts non possédés on privilégiera les actions `ForceSpell` ou `ReallyForceSpell` ou encore `ForceSpellDead`.\
Le choix de l'action impacte fortement le choix des contraintes du block `IF`.


## Silence

```{note}
Concerne les actions :
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

Il existe une solution à ces deux problèmatiques, que l'on utilise déjà sans le savoir : `HaveSpell`.\
Celui-ci vérifie que :
- le sort est mémorisé
- si le sort nécessite une composante verbale, le lanceur de sort n'est pas sous l'effet d'un silence
- le lanceur n'est pas sous l'effet empêchant le lancement de sort (comme celle dans la Transformation de Tenser par exemple)

Oui, `HaveSpell(XX)` peut renvoyer faux même si le sort est mémorisé.

Cela signifie que la présence d'une composante verbale est checkée au moment de le lancer :
- cela gère donc les modifications faites par les mods
- pas d'erreur d'inattention puisqu'il suffit de ne pas mettre `StateCheck(Myself, STATE_SILENCED)`


Ainsi, le meilleur moyen de gérer le silence, c'est de laisser l'action `HaveSpell` faire le travail.


## Blocage de la capacité à lancer des sorts

```{note}
Concerne les actions :
- `Spell`
```

Infligé par l'opcode 145, cet effet est géré automatiquement par `HaveSpell`.\
Voir le point précédent pour plus d'informations.

## Poison

```{note}
Concerne les actions :
- `Spell`
```

Un désastre pour les magiciens, le poison lui inflige des dégâts sur la durée. Double effet kisscool :
1. Il n'a pas beaucoup de points de vie donc c'est vite dramatique
2. Cela affecte clairement sa capacité à lancer des sorts (et de façon imprévisible)

Le comble étant qu'il existe des sorts pour se soigner mais qu'elle est leur probabilité de réussite ?

### État des lieux des autres mods

* ep scripts : un mage empoisonné ne lance plus aucun sort
* bddefai : yolo on tente quand même
* uScript v2 : yolo sauf pour Contagion


La raison pour laquelle les moddeurs y répondent par des solutions opposées c'est qu'il n'y a pas de réponse.\
Mais tentons une analyse.


### TheoryCrafting sur le poison


Le poison frappe avec une fréquence fixe pouvant provoquer aléatoirement à chaque fois une interrumption de l'incantation du sort.

Lancer un sort est donc risqué car la probabilité d'échec est réelle mais… est-ce moins risqué de ne lancer AUCUN sort pendant 30 secondes ?

Idéalement il faudrait incanter le sort entre deux dégâts afin de ne pas être inquiété. Sauf que l'on ne connait pas la fréquence à l'avance.


Voici quelques chiffres approximatifs :

```{note}
- Répartition des poison en fonction de leur fréquence d'apparition :
  * 1 seconde : 75%
  * 2 secondes : 5%
  * 3 secondes : 20%

- Temps nécessaire à l'incantation des sorts : valeur * 0.6 secondes

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


### Snippet
Pour s'assurer qu'un personnage n'est pas empoisonné ou qu'il ne puisse en subir les dégâts :
```cr
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)
```


## Maladie

```{note}
Concerne les actions :
- `Spell`
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
Mais sans certitude que la maladie inflige bien des dommages.


## Mages cagoulés d'Athkatla

Lire [cette page](/general/athkatla_magec).


## Hiatus entropiques

```{note}
Concerne les actions :
- `Spell`
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


## Zone anti-magie et autre modificateur de réussite à l'incantation

TODO

## Temps d'attente entre chaque incantation/utilisation d'objet

Concerne toutes les actions sauf `ReallyForceSpell`.
TODO
