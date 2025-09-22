# ✅ Can Hide

Snippet qui permet au personnage de se camoufler.\
Toujours utilisable quelque soit l'allégeance du personnage.

`````{tab-set}
````{tab-item} Base
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 2)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !StateCheck(Myself, STATE_INVISIBLE)
    CheckStat(Myself, 0, SANCTUARY)
    OR(2)
        !CheckSpellState(Myself, CANNOT_TURN_INVISIBLE)
        StateCheck(Myself, STATE_NONDETECTION)
    !ButtonDisabled(BUTTON_STEALTH)
    !ModalState(STEALTH)
    OR(2)
        CheckStatGT(Myself, 49, HIDEINSHADOWS)
        CheckStatGT(Myself, 49, STEALTH)
    OR(3)
        Allegiance(Myself, NOTEVIL)
        !See([GOODCUTOFF])
        Kit(Myself, SHADOWDANCER)
    OR(3)
        Allegiance(Myself, EVILCUTOFF)
        !See([EVILCUTOFF])
        Kit(Myself, SHADOWDANCER)
THEN
    RESPONSE #1
        Hide()
END
```
Tous les personnages peuvent se camoufler, il n'y a aucune restriction de classe à cela.\
C'est le niveau de maîtrise qui limite fortement l'utilisation de cette action.

`NearestEnemyOf` fonctionne mal pour le cas des personnages `NEUTRAL`.\
De plus, `See([...])` permet de filtrer naturellement les personnages avec un `STATE_SLEEPING` et `STATE_DEAD` actif, c'est l'idéal car la présence de ces personnages ne bloque pas l'action.
````

````{tab-item} Lumière
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 2)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !StateCheck(Myself, STATE_INVISIBLE)
    CheckStat(Myself, 0, SANCTUARY)
    OR(2)
        !CheckSpellState(Myself, CANNOT_TURN_INVISIBLE)
        StateCheck(Myself, STATE_NONDETECTION)
    !ButtonDisabled(BUTTON_STEALTH)
    !ModalState(STEALTH)
    OR(2)
        CheckStatGT(Myself, 49, HIDEINSHADOWS)
        CheckStatGT(Myself, 49, STEALTH)
    OR(4)
        CheckStatGT(Myself, 99, HIDEINSHADOWS)
        CheckStatGT(Myself, 99, STEALTH)
        !TimeOfDay(DAY)
        !AreaType(OUTDOOR)
    OR(3)
        Allegiance(Myself, NOTEVIL)
        !See([GOODCUTOFF])
        Kit(Myself, SHADOWDANCER)
    OR(3)
        Allegiance(Myself, EVILCUTOFF)
        !See([EVILCUTOFF])
        Kit(Myself, SHADOWDANCER)
THEN
    RESPONSE #1
        Hide()
END
```
L'environnement extérieur influence la probabilité de réussite du camouflage.\
Il est deux fois plus difficile de se camoufler de jour en extérieur.
````

````{tab-item} Shadowdancer
```cr
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 2)
    !StateCheck(Myself, STATE_DISABLED | STATE_REALLY_DEAD)
    CheckStat(Myself, 0, CASTERHOLD)
    !StateCheck(Myself, STATE_INVISIBLE)
    CheckStat(Myself, 0, SANCTUARY)
    OR(2)
        !CheckSpellState(Myself, CANNOT_TURN_INVISIBLE)
        StateCheck(Myself, STATE_NONDETECTION)
    !ButtonDisabled(BUTTON_STEALTH)
    !ModalState(STEALTH)
    OR(2)
        CheckStatGT(Myself, 49, HIDEINSHADOWS)
        CheckStatGT(Myself, 49, STEALTH)
    OR(4)
        CheckStatGT(Myself, 99, HIDEINSHADOWS)
        CheckStatGT(Myself, 99, STEALTH)
        !TimeOfDay(DAY)
        !AreaType(OUTDOOR)
    Kit(Myself, SHADOWDANCER)
    Allegiance(Myself, NOTEVIL)
    OR(2)
        !See([EVILCUTOFF])
        CheckStat(LastSeenBy(Myself), 0, SEEINVISIBLE)
    OR(2)
        !See(SecondNearest([EVILCUTOFF]))
        CheckStat(LastSeenBy(Myself), 0, SEEINVISIBLE)
    OR(2)
        !See(ThirdNearest([EVILCUTOFF]))
        CheckStat(LastSeenBy(Myself), 0, SEEINVISIBLE)
THEN
    RESPONSE #1
        Hide()
END
```
Gérer spécifiquement le kit du Maître des ombres facilite les contraintes et permet donc d'aller un peu plus loin.\
Car s'il peut se camoufler au beau milieu d'un champ de bataille.\
Encore faut-il que les personnages ennemis puissent ne pas voir l'invisible.\
Le fonctionnement est assez proche de celui d'une potion d'invisibilité.
````
`````
