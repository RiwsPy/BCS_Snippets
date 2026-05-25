# ✔️ Modal swap

Il est agréable de configurer une touche pour changer de modale active d'un personnage.\
Passer du mode `détection des pièges` à `furtivité` par exemple.

Ici, on réutilise les variables du script générique `bddefai`.\
De plus, on regarde les maîtrises du personnage pour savoir s'il possède des points dans la compétence et on vérifie également que le bouton correspondant est bien actif.

La vérification du bouton est plus dynamique que la vérification des kits pour bloquer l'accès à une modale car cela fonctionne également pour les kits (et objets) ajoutés ou modifiés par des mods.


`````{tab-set}
````{tab-item} Alliés
```cr
IF
    HotKey("X")
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
    !ButtonDisabled(14)
    OR(2)
        CheckStatGT(Myself, 0, TRAPS)
        CheckStatGT(Myself, 0, DETECTILLUSIONS)
    OR(3)
        Class(Myself, THIEF_ALL)
        Class(Myself, MONK)
        Class(Myself, SHAMAN)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 1)
END

IF
    HotKey("X")
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 2)
    !ButtonDisabled(BUTTON_STEALTH)
    OR(2)
        CheckStatGT(Myself, 0, HIDEINSHADOWS)
        CheckStatGT(Myself, 0, STEALTH)
    OR(3)
        Class(Myself, THIEF_ALL)
        Class(Myself, RANGER_ALL)
        Class(Myself, MONK)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 2)
END

IF
    HotKey("X")
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 3)
    !ButtonDisabled(BUTTON_BATTLESONG)
    OR(2)
        Class(Myself, BARD_ALL)
        Class(Myself, SHAMAN)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 3)
END

IF
    HotKey("X")
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 4)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    CheckStatGT(Myself, 0, TURNUNDEADLEVEL)
    OR(2)
        Class(Myself, CLERIC_ALL)
        Class(Myself, PALADIN_ALL)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 4)
END

IF
    HotKey("X")
    !Global("BDAI_SKILL_MODE", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 0)
END

```
````

````{tab-item} Non-alliés
```cr
IF
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
    !ButtonDisabled(14)
    Allegiance(Myself, NOTGOOD)
    OR(2)
        CheckStatGT(Myself, 0, TRAPS)
        CheckStatGT(Myself, 0, DETECTILLUSIONS)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 1)
END

IF
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
    !ButtonDisabled(BUTTON_STEALTH)
    Allegiance(Myself, NOTGOOD)
    OR(2)
        CheckStatGT(Myself, 0, HIDEINSHADOWS)
        CheckStatGT(Myself, 0, STEALTH)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 2)
END

IF
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
    !ButtonDisabled(BUTTON_BATTLESONG)
    Allegiance(Myself, NOTGOOD)
    OR(2)
        Class(Myself, BARD_ALL)
        Class(Myself, SHAMAN)
    OR(2)
        Allegiance(Myself, NOTEVIL)
        Class(Myself, SHAMAN)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 3)
END

IF
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    Allegiance(Myself, NOTGOOD)
    CheckStatGT(Myself, 0, TURNUNDEADLEVEL)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 4)
END


```

Le trigger `HotKey` n'est pas adapté pour les non-alliés.\
De plus, les contraintes de classes s'appliquent difficilement à des personnages non joueurs.\
Cas particulier du Barde, dont le chant bénéficie toujours aux membres du groupe, on le fera chanter au mieux s'il n'est pas ennemi.
````

````{tab-item} Générique
```cr
IF
    OR(2)
        HotKey("X")
        Allegiance(Myself, NOTGOOD)
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
    !ButtonDisabled(14)
    OR(2)
        CheckStatGT(Myself, 0, TRAPS)
        CheckStatGT(Myself, 0, DETECTILLUSIONS)
    OR(4)
        Class(Myself, THIEF_ALL)
        Class(Myself, MONK)
        Class(Myself, SHAMAN)
        Allegiance(Myself, NOTGOOD)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 1)
END

IF
    OR(2)
        HotKey("X")
        Allegiance(Myself, NOTGOOD)
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 2)
    OR(2)
        GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
        Allegiance(Myself, GOODCUTOFF)
    !ButtonDisabled(BUTTON_STEALTH)
    OR(2)
        CheckStatGT(Myself, 0, HIDEINSHADOWS)
        CheckStatGT(Myself, 0, STEALTH)
    OR(4)
        Class(Myself, THIEF_ALL)
        Class(Myself, RANGER_ALL)
        Class(Myself, MONK)
        Allegiance(Myself, NOTGOOD)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 2)
END

IF
    OR(2)
        HotKey("X")
        Allegiance(Myself, NOTGOOD)
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 3)
    OR(2)
        GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
        Allegiance(Myself, GOODCUTOFF)
    !ButtonDisabled(BUTTON_BATTLESONG)
    OR(2)
        Class(Myself, BARD_ALL)
        Class(Myself, SHAMAN)
    OR(2)
        Allegiance(Myself, NOTEVIL)
        Class(Myself, SHAMAN)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 3)
END

IF
    OR(2)
        HotKey("X")
        Allegiance(Myself, NOTGOOD)
    GlobalLT("BDAI_SKILL_MODE", "LOCALS", 4)
    OR(2)
        GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
        Allegiance(Myself, GOODCUTOFF)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    CheckStatGT(Myself, 0, TURNUNDEADLEVEL)
    OR(3)
        Class(Myself, CLERIC_ALL)
        Class(Myself, PALADIN_ALL)
        Allegiance(Myself, NOTGOOD)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 4)
END

IF
    HotKey("X")
    !Global("BDAI_SKILL_MODE", "LOCALS", 0)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 0)
END
```
````

`````
