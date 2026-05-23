# ✔️ Modal swap

Il est agréable de configurer une touche pour changer de modale active d'un personnage.\
Passer du mode `détection des pièges` à `furtivité` par exemple.

Ici, on réutilise les variables du script générique `bddefai`.\
De plus, on regarde les maîtrises du personnage pour savoir s'il possède des points dans la compétence et on vérifie également que le bouton correspondant est bien actif.

La vérification du bouton est plus dynamique que la vérification des kits pour bloquer l'accès à une modale car cela fonctionne également pour les kits (et objets) ajoutés ou modifiés par des mods.


`````{tab-set}
````{tab-item} Basique
```cr
IF
    HotKey("X")
    OR(2)
        GlobalLT("BDAI_SKILL_MODE", "LOCALS", 1)
        GlobalGT("BDAI_SKILL_MODE", "LOCALS", 4)
    !ButtonDisabled(14)
    OR(3)
        Class(Myself, THIEF_ALL)
        Class(Myself, MONK)
        Class(Myself, SHAMAN)
    OR(2)
        CheckStatGT(Myself, 0, TRAPS)
        CheckStatGT(Myself, 0, DETECTILLUSIONS)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 1)
END

IF
    HotKey("X")
    OR(2)
        GlobalLT("BDAI_SKILL_MODE", "LOCALS", 2)
        GlobalGT("BDAI_SKILL_MODE", "LOCALS", 4)
    !ButtonDisabled(BUTTON_STEALTH)
    OR(3)
        Class(Myself, THIEF_ALL)
        Class(Myself, RANGER_ALL)
        Class(Myself, MONK)
    OR(2)
        CheckStatGT(Myself, 0, HIDEINSHADOWS)
        CheckStatGT(Myself, 0, STEALTH)
THEN
    RESPONSE #1
        SetGlobal("BDAI_SKILL_MODE", "LOCALS", 2)
END

IF
    HotKey("X")
    OR(2)
        GlobalLT("BDAI_SKILL_MODE", "LOCALS", 3)
        GlobalGT("BDAI_SKILL_MODE", "LOCALS", 4)
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
    !Global("BDAI_SKILL_MODE", "LOCALS", 4)
    !ButtonDisabled(BUTTON_TURNUNDEAD)
    OR(2)
        Class(Myself, CLERIC_ALL)
        Class(Myself, PALADIN_ALL)
    CheckStatGT(Myself, 0, TURNUNDEADLEVEL)
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
