# ✅ Can PickPocket

Snippet qui permet de savoir si le vol à la tire est envisageable ou non.

Utilisable quelque soit l'allégeance de la créature.\
Il n'est pas limité aux "Voleurs" par contre un minimum de maîtrise est nécessaire (valeur minimale dans SLTSTEAL.2DA sur la version Vanilla).

```vb.net
IF
    ActionListEmpty()
    Global("BDAI_SKILL_MODE", "LOCALS", 0)
    !DisabledButton(BUTTON_THIEVING)
    CheckStatGT(Myself, 9, PICKPOCKET) //'' Facultatif mais conseillé
    !StateCheck(Myself, STATE_INVISIBLE) //'' Facultatif, à savoir que le larçin est une action qui dissipe l'invisibilité
    !InventoryFull(Myself)

    See([...])

    Range(LastSeenBy(Myself), 2) //'' Facultatif, à savoir que dans le cas contraire, le personnage se déplacera
    !CheckStat(LastSeenBy(Myself), 255, PICKPOCKET) //'' Facultatif, pas très RP
    OR(2) //'' Un NOTEVIL ne peut voler un EVILCUTOFF et inversement
        Allegiance(Myself, NOTEVIL)
        Allegiance(LastSeenBy(Myself), NOTGOOD)
    OR(2)
        Allegiance(Myself, EVILCUTOFF)
        Allegiance(LastSeenBy(Myself), NOTEVIL)
    HasItemType(LastSeenBy(Myself), DROPPABLE)
    ...
THEN
```

## Limites

- Des objets peuvent être considérés comme volables mais placés dans des slots qui ne peuvent être volés (celui des bottes par exemple).
