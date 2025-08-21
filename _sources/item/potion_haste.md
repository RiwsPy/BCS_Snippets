# Huile de rapidité (POTN14)

On hérite du snippet des [Objets rapides](/item/base).

`````{tab-set}
````{tab-item} Basique
```cr
IF
    //# Snippet Objet rapide
    !StateCheck(Myself, STATE_HASTED)
    See(NearestEnemyOf(Myself))
THEN
```
On vérifie que :
- un effet similaire ne soit pas déjà en cours
- un ennemi est vu
````

````{tab-item} Spécifique
```cr
IF
    //# Snippet Objet rapide
    !StateCheck(Myself, STATE_HASTED | STATE_BLIND)
    HasWeaponEquiped(Myself)
    OR(2)
        Allegiance(Myself, GOODCUTOFF)
        NumCreatureGT([GOODCUTOFF], 4)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        NumCreatureGT([EVILCUTOFF], 4)
THEN
```

On reprend la précédente en ajoutant :
- Le personnage ne doit pas être aveuglé (malus de toucher et difficulté à trouver des cibles)
- Une arme doit être équipée (cela filtre aussi un peu les moines, qui ne devraient pas pouvoir l'utiliser)
- On remplace le trigger `See` par `NumCreature`, imposant un nombre plus important d'ennemi (la potion dure 5 tours)
````

````{tab-item} Poussive
```cr
IF
    //# Snippet Objet rapide
    !StateCheck(Myself, STATE_HASTED | STATE_BLIND)
    !Class(Myself, MAGE)
    CheckStatGT(Myself, 7, STR)
    HasWeaponEquiped(Myself)
    CheckStatLT(Myself, 2, ENCUMBERANCE)
    !CheckSpellState(Myself, DEFENSIVE_SPIN)
    !CheckSpellState(Myself, OFFENSIVE_SPIN)

    CheckStat(Myself, 0, CLERIC_INSECT_PLAGUE)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)

    OR(2)
        Allegiance(Myself, GOODCUTOFF)
        NumCreatureGT([GOODCUTOFF], 4)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        NumCreatureGT([EVILCUTOFF], 4)
THEN
```

On reprend la précédente en ajoutant :
- Le personnage n'est pas un mage pur (il ne frappe pas)
- Le personnage a une force minimale de 8 (pas de malus au toucher)
- Le personnage n'est pas immobilisé par surcharge (sa vitesse de déplacement étant nulle)
- Le personnage n'est pas empoisonné ou grignoté par des insectes (sinon cela double la fréquence des dégâts)
- Le personnage n'est pas immunisé contre la hâte (incomplet)
````

````{tab-item} Fuite
```cr
IF
    //# Snippet Objet rapide
    !StateCheck(Myself, STATE_HASTED)
    CheckStatLT(Myself, 2, ENCUMBERANCE)
    !CheckSpellState(Myself, DEFENSIVE_SPIN)
    !CheckSpellState(Myself, OFFENSIVE_SPIN)
    HPPercentLT(Myself, 15)
    See(NearestEnemyOf(Myself))
THEN
    RESPONSE #1
        UseItem("POTN14", Myself)
        RunAwayFromNoInterrupt(LastSeenBy(Myself), 300)
END
```
Parce que cette potion permet aussi de courir plus vite.
- Le personnage profite de ce bonus pour s'enfuir
````


````{tab-item} Repli régénérant
```cr
IF
    //# Snippet Objet rapide
    HasItemEquiped("POTN42", Myself)
    !StateCheck(Myself, STATE_HASTED)
    CheckStatLT(Myself, 2, ENCUMBERANCE)
    !CheckSpellState(Myself, DEFENSIVE_SPIN)
    !CheckSpellState(Myself, OFFENSIVE_SPIN)
    HPPercentLT(Myself, 25)

    CheckStat(Myself, 0, CLERIC_INSECT_PLAGUE)
    OR(2)
        !StateCheck(Myself, STATE_POISONED)
        CheckStatGT(Myself, 99, RESISTPOISON)

    See(NearestEnemyOf(Myself))
THEN
    RESPONSE #1
        UseItem("POTN14", Myself)
        RunAwayFromNoInterrupt(LastSeenBy(Myself), 100)
        UseItem("POTN42", Myself)
        RunAwayFromNoInterrupt(LastSeenBy(Myself), 300)
END
```
Parce que cette potion permet de courir et de régénérer plus vite.
- Le personnage profite de ce bonus pour reculer et boire une potion de régénération donc l'efficacité sera doublée
````

`````
