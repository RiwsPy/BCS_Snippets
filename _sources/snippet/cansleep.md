# ✔️ Enemies can Rest

Il n'y a pas que les membres du groupe qui devrait pouvoir se reposer.\
De la même façon, on aimerait que tous les personnages puissent se souvenir de ses sorts et se soigner pour commencer la journée du bon pied.


## Quelles conséquences au repos ?

Se reposer, oui. Mais avec quelles conséquences ?\
Habituellement pour le groupe, cela permet au groupe de se soigner, de se remémorer les sorts utilisés et de recharger les objets.\
Mais les membres du groupe peuvent aussi profiter de la pause pour refaire le plein de potions et de munitions.


`````{tab-set}
````{tab-item} Classique
* ❓ Soin des blessures
* ✅ Remémorisation des sorts et rechargement des objets
* ❌ Réinitialisation des variables de combat
* ✅ Conservation des variables hors combat
* ❌ Complétion du stock d'objets
* ✅ Solution générique
```cr
IF
    //# …
THEN
    RESPONSE #1
        Rest()
        //# …
END
```

Cela restaure les sorts et les capacités d'équipement, cela peut soigner.\
On peut compléter en réinitialisant manuellement les variables de combat (utilisation d'objets, déclencheurs…).
````

````{tab-item} Avec soin
* ✅ Soin des blessures
* ✅ Remémorisation des sorts et rechargement des objets
* ❌ Réinitialisation des variables de combat
* ✅ Conservation des variables hors combat
* ❌ Complétion du stock d'objets
* ✅ Solution générique
```cr
IF
    //# …
THEN
    RESPONSE #1
        ApplySpell(Myself, RESTORE_FULL_HEALTH)
        Rest()
        //# …
END
```

`RESTORE_FULL_HEALTH` rend toute la santé, soigne du poison, de la maladie et de la débilité mentale.
````

````{tab-item} Totale
* ✅ Soin des blessures
* ✅ Remémorisation des sorts et rechargement des objets
* ✅ Réinitialisation des variables de combat
* ❌ Conservation des variables hors combat
* ✅ Complétion du stock d'objets
* ❌ Solution générique
```cr
IF
    !InParty(Myself)
    //# …
THEN
    RESPONSE #1
        ChangeAnimationNoEffect("TODO")
END
```

⚠️ À utiliser en connaissance de cause.\
Cette méthode permet de réinitialiser totalement le personnage en une seule ligne.\
C'est notamment pratique pour ne pas oublier des variables ou objets, notamment ceux ajoutés/modifiés par les autres mods.

Elle a deux impacts majeurs :
* le script doit être spécifique à une animation de créature ou gérer unitairement les différents cas
* On perd les variables sans rapport avec le combat, `NumTimesTalkedTo`, les objets volés ou donnés sont restaurés…
````

`````


## Méthodologies de repos

`````{tab-set}
````{tab-item} Simple

```cr
IF
    !ActuallyInCombat()
    !InParty(Myself)
    OnCreation()
THEN
    RESPONSE #1
        // TODO: méthode choisie
        Continue()
END
```
Solution en un seul bloc sans timer ni variable.\
Le personnage est reposé à chaque rechargement de la zone où il est présent.

````

````{tab-item} Avec timer
```cr
// Initialisation
IF
    OnCreation()
    Global("BCS_NeedResting", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobalTimer("BCS_NeedResting", "LOCALS", EIGHT_HOURS)
        Continue()
END

IF
    !ActuallyInCombat()
    !InParty(Myself)
    GlobalTimerExpired("BCS_NeedResting", "LOCALS")
THEN
    RESPONSE #1
        SetGlobalTimer("BCS_NeedResting", "LOCALS", EIGHT_HOURS)
        // TODO: méthode choisie
END
```

Le personnage se repose toutes les 8 heures (hors combat).\
Indépendamment du fait que le groupe se soit reposé ou non.

````


````{tab-item} PartyRested
```cr
// Baldur.bcs
IF
    PartyRested()
THEN
    RESPONSE #1
        IncrementGlobal("BCS_PARTY_REST_COUNT", "GLOBAL", 1)
        Continue()
END
//

// Script de la créature
// variables non synchronisées, initialisation ou membre du groupe: pas de repos et on synchronise les variables
IF
    OR(2)
        Global("BCS_%NAME%_REST_COUNT", "GLOBAL", 0)
        InParty(Myself)
    !GlobalsEqual("BCS_%NAME%_REST_COUNT", "BCS_PARTY_REST_COUNT")
THEN
    RESPONSE #1
        SetGlobal("BCS_%NAME%_REST_COUNT", "GLOBAL", 0)
        SmallWait(0)
        AddGlobals("BCS_%NAME%_REST_COUNT", "BCS_PARTY_REST_COUNT")
END

IF
    !Global("BCS_%NAME%_REST_COUNT", "GLOBAL", 0)
    !GlobalsEqual("BCS_%NAME%_REST_COUNT", "BCS_PARTY_REST_COUNT")
    !InParty(Myself)
THEN
    RESPONSE #1
        SetGlobal("BCS_%NAME%_REST_COUNT", "GLOBAL", 0)
        // TODO: méthode choisie
        AddGlobals("BCS_%NAME%_REST_COUNT", "BCS_PARTY_REST_COUNT")
END

```
Si on veut vraiment faire corréler le repos des créatures avec le repos du groupe.\
Beaucoup plus contraignant.\
On ruse avec l'utilisation de GLOBAL qui s'incrémente à chaque repos du groupe.\
Fonctionnel pour un script spécifique à un personnage ou couplé avec WeiDU (si nom unique).

````
`````

## Informations

- PartyRested() n'est pas capté par les personnages hors du groupe
