# ✔️ Comparaison d'allégeance

Must have des scripts génériques.\
Savoir si la créature active et la cible ont la même allégeance ou une allégeance opposée.\
On peut même gérer le cas des NEUTRAL.

`````{tab-set}
````{tab-item} Même allégiance
```cr
    See([ANYONE])
    OR(2)
        Allegiance(Myself, GOODCUTOFF)
        Allegiance(LastSeenBy(Myself), EVILCUTOFF)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        Allegiance(LastSeenBy(Myself), GOODCUTOFF)
```

Les GOODCUTOFF ciblent les GOODCUTOFF.\
Les EVILCUTOFF ciblent les EVILCUTOFF.
````

````{tab-item} Même allégiance complet
```cr
    See([ANYONE])
    OR(2)
        Allegiance(Myself, GOODCUTOFF)
        Allegiance(LastSeenBy(Myself), NOTGOOD)
    OR(2)
        Allegiance(Myself, NOTGOOD)
        Allegiance(LastSeenBy(Myself), GOODCUTOFF)
    OR(2)
        Allegiance(Myself, NOTEVIL)
        Allegiance(LastSeenBy(Myself), EVILCUTOFF)
    OR(2)
        Allegiance(Myself, NOTNEUTRAL)
        Allegiance(LastSeenBy(Myself), NOTEVIL)
```

Les GOODCUTOFF ciblent les GOODCUTOFF.\
Les EVILCUTOFF ciblent les EVILCUTOFF.\
Les NEUTRAL ciblent les NEUTRAL.
````

````{tab-item} Allégiance opposée
```cr
    See([ANYONE])
    OR(2)
        Allegiance(Myself, GOODCUTOFF)
        Allegiance(LastSeenBy(Myself), GOODCUTOFF)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        Allegiance(LastSeenBy(Myself), EVILCUTOFF)
```

Les GOODCUTOFF ciblent les EVILCUTOFF.\
Les EVILCUTOFF ciblent les GOODCUTOFF.
````

````{tab-item} EVIL vs NOTEVIL
```cr
    See([ANYONE])
    OR(2)
        Allegiance(Myself, NOTEVIL)
        Allegiance(LastSeenBy(Myself), NOTEVIL)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        Allegiance(LastSeenBy(Myself), EVILCUTOFF)
```

Les NOTEVIL ciblent les EVILCUTOFF.\
Les EVILCUTOFF ciblent les NOTEVIL.
````

````{tab-item} GOOD vs NOTGOOD
```cr
    See([ANYONE])
    OR(2)
        Allegiance(Myself, NOTGOOD)
        Allegiance(LastSeenBy(Myself), NOTGOOD)
    OR(2)
        Allegiance(Myself, GOODCUTOFF)
        Allegiance(LastSeenBy(Myself), GOODCUTOFF)
```

Les NOTGOOD ciblent les GOODCUTOFF.\
Les GOODCUTOFF ciblent les NOTGOOD.
````
`````
