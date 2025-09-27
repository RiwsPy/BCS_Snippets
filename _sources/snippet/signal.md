# ✔️ Signaux génériques

Un signal est un condition qui va permettre, s'il est capté, aux créatures d'activer un comportement spéficique.\
Le jeu en possède plusieurs comme `HitBy`, `Help`, `OnCreation`...\
Ils possèdent deux limites :
1. Ils sont pour la plupart spécifique à la créature
2. Si on rate le signal, c'est trop tard


On utilisant les variables `GLOBAL`, on a une alternative pour arriver à nos fins.

`````{tab-set}
````{tab-item} Classique
```cr
//# Script quelqueconque
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_IsActive", "GLOBAL", 1)
        //# TODO: logique
        SetGlobal("BCS_SIGNAL_IsActive", "GLOBAL", 0)
END

//# Script du personnage
//# On active la réponse au signal
IF
    Global("BCS_SIGNAL_IsActive", "GLOBAL", 1)
    Global("BCS_SIGNAL_IsActived", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_IsActived", "LOCALS", 1)
        //# TODO: logique
END

//# À la fin du signal on désactive la variable LOCALS
IF
    Global("BCS_SIGNAL_IsActive", "GLOBAL", 0)
    !Global("BCS_SIGNAL_IsActived", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_IsActived", "LOCALS", 0)
END
```
On active le script qu'une seule fois par créature.\
L'activation et la désactivation de la variable `GLOBAL` doivent être gérées manuellement (pas nécessairement dans le même bloc).
````

````{tab-item} Timer
```cr
//# Script quelqueconque
THEN
    RESPONSE #1
        SetGlobalTimer("BCS_SIGNAL_Timer", "GLOBAL", ONE_ROUND)
END

//# Script du personnage
//# On active la réponse au signal
IF
    !GlobalTimerExpired("BCS_SIGNAL_Timer", "GLOBAL")
    Global("BCS_SIGNAL_IsActived", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_IsActived", "LOCALS", 1)
        //# TODO: logique
END

//# À la fin du signal on désactive la variable LOCALS
IF
    !GlobalTimerNotExpired("BCS_SIGNAL_Timer", "GLOBAL", 0)
    !Global("BCS_SIGNAL_IsActived", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_IsActived", "LOCALS", 0)
END
```
Il n'est pas toujours possible ou pas toujours adapté de terminer le signal, mais on peut le limiter dans le temps.\
On active le script qu'une seule fois par créature.\
Le signal est détectable par les créatures pendant une durée définie (ici 1 round).\
Une fois le temps écoulé, le signal s'arrête de lui-même.
````

````{tab-item} VIP
```cr
//# Script quelqueconque
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_Counter", "GLOBAL", 5)
END

//# Script du personnage
//# On active la réponse au signal
IF
    GlobalGT("BCS_SIGNAL_Counter", "GLOBAL", 0)
    Global("BCS_SIGNAL_IsActived", "LOCALS", 0)
THEN
    RESPONSE #1
        IncrementGlobal("BCS_SIGNAL_Counter", "GLOBAL", -1)
        SetGlobal("BCS_SIGNAL_IsActived", "LOCALS", 1)
        //# TODO: logique
END

//# À la fin du signal on désactive la variable LOCALS
IF
    Global("BCS_SIGNAL_Counter", "GLOBAL", 0)
    !Global("BCS_SIGNAL_IsActived", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_IsActived", "LOCALS", 0)
END
```
On active le script qu'une seule fois par créature.\
Seules les X (ici 5) premières créatures pourront profiter de l'effet.\
Une fois que X créatures différentes ont validées les conditions, le signal s'arrête de lui-même.
````

````{tab-item} VIP - Ration double
```cr
//# Script quelqueconque
THEN
    RESPONSE #1
        SetGlobal("BCS_SIGNAL_Counter", "GLOBAL", 5)
END

//# Script du personnage
//# On active la réponse au signal
IF
    GlobalGT("BCS_SIGNAL_Counter", "GLOBAL", 0)
THEN
    RESPONSE #1
        IncrementGlobal("BCS_SIGNAL_Counter", "GLOBAL", -1)
        //# TODO: logique
END
```
Il n'y a plus de limite d'activation par créature.\
L'effet pourra être distribué que X fois (ici 5).\
Une fois que les X effets ont été donnés, le signal s'arrête de lui-même.\
Ce principe est compatible avec les 3 snippets précédents.
````


`````
