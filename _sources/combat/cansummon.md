# ⚠️ Can Summon Creature

Invoquer des créatures est assez facile mais une limite de quantité s'impose aux membres du groupe.
Il y a deux types d'invocations :
1. Les convocations classiques
1. Les convocations de solaires

`````{tab-set}
````{tab-item} Non groupé
```cr

IF
    //# …
    !InParty(Myself)
    CheckStat(Myself, -1, PUPPETMASTERID)
THEN

```

Les restrictions s'appliquent aux membres du groupe et à leurs copies (image projetée, simulacre...).\
Pour les autres : il n'y a pas de limite.
````

````{tab-item} Classique
```cr

IF
    //# …
    OR(2)
        InParty(Myself)
        !CheckStat(Myself, -1, PUPPETMASTERID)
    NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 5)
    NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 5)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 4)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 1)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 3)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 2)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 3)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 1)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 4)
THEN

```

Pour les membres du groupe, jusqu'à 5 créatures peuvent être invoquées en parallèle.\
Est considéré comme invocations les créatures de genre `SUMMONED` et `SUMMONED_DEMON`.\
Comme on ne peut pas additionner leur nombre, il faut ruser.
````

````{tab-item} Solaire 1
```cr

IF
    //# …
    OR(2)
        InParty(Myself)
        !CheckStat(Myself, -1, PUPPETMASTERID)
    NumCreature([GOODCUTOFF.0.0.0.0.BOTH], 0)
    NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 5)
    NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 5)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 4)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 1)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 3)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 2)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 3)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 1)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 4)
THEN

```

La base est très proche des convocations classiques.\
Excepté qu'une seule solaire peut-être présente à la fois.

Une solaire doit posséder le genre `BOTH`.\
⚠️ : aucun test n'est possible sur l'animation de la créature pour confirmer qu'il s'agit bien d'une solaire
````
````{tab-item} Solaire 2
```cr

IF
    //# …
    OR(2)
        InParty(Myself)
        !CheckStat(Myself, -1, PUPPETMASTERID)
    NumCreature([GOODCUTOFF.0.SOLAR.0.0.BOTH], 0)
    NumCreature([GOODCUTOFF.0.ANTISOLAR.0.0.BOTH], 0)
    NumCreature([GOODCUTOFF.0.PLANATAR.0.0.BOTH], 0)
    NumCreature([GOODCUTOFF.0.DARKPLANATAR.0.0.BOTH], 0)
    NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 5)
    NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 5)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 4)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 1)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 3)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 2)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 3)
    OR(2)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED], 1)
        NumCreatureLT([GOODCUTOFF.0.0.0.0.SUMMONED_DEMON], 4)
THEN

```

La base est très proche des convocations classiques.\
Excepté qu'une seule solaire peut-être présente à la fois.

Une solaire doit posséder le genre `BOTH`.\
⚠️ : plutôt que de tester l'animation du personnage, il est possible de tester sa classe, cela est conforme pour la version vanilla mais ce n'est pas assuré pour les versions moddées.
````
`````

Les snippets sont optimisés pour l'invocation d'une seule créature.

⚠️ plusieurs limites récurrentes :
1. les créatures hors du champ de vision de l'invocateur ne sont pas prises en compte
1. la limitation n'est pas dynamique, il est possible de modifier `SUMMLIMT.2da` et dans ce cas, le snippet sera inadapté
1. l'EA `GOODCUTOFF` est trop large, il faudrait plutôt tester EA <= 15
