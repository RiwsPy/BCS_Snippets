# ✅ GlobalExists

`Global("var", "X", 0)` vaut vrai dans deux cas :
1. la variable vaut 0
1. la variable n'est pas définie

Ce snippet permet de différencier ces deux cas.\
C'est notamment utile pour tester les variables de la version vanilla que l'on ne peut pas modifier ou celles des autres mods sur lesquelles on a pas la main.


## GlobalExists

```vb.net
IF
    OR(2)
        !Global("var", "GLOBAL", 0)
        GlobalTimerExpired("var", "GLOBAL")
THEN
```


## GlobalNotExists

```vb.net
IF
    Global("var", "GLOBAL", 0)
    !GlobalTimerExpired("var", "GLOBAL")
THEN
```



## Exemple

La string ne s'affichera qu'une seule fois :
```vb.net
IF
    Global("var", "LOCALS", 0)
    !GlobalTimerExpired("var", "LOCALS")
THEN
    RESPONSE #1
        DisplayStringHead(Myself, 1)
        SetGlobal("var", "LOCALS", 0)
END
```

## Pourquoi ça marche ?

Mixer des méthodes qui jouent avec des variables et des timers peut être déroutant au premier abord.\
Il faut savoir que les timers ne sont rien d'autre que des variables.

- `Global("var", "LOCALS", 0)` nous permet de savoir que la variable :
    - n'existe pas
    - ou vaut 0
- `!GlobalTimerExpired` nous permet de savoir que la variable :
    - n'existe pas
    - ou que sa valeur est supérieure au temps du jeu

Comme la variable ne peut valoir 0 et être supérieure au temps de jeu, alors, la seule raison pour que le bloc s'exécute, c'est que la variable n'existe pas.\
Et comme `SetGlobal("var", "LOCALS", 0)` la créé, alors le bloc ne s'éxécute qu'une fois.
