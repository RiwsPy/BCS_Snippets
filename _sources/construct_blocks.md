# Ordonner un bloc



Dans un bloc, notamment dans ceux de combat, il y a trois types d'informations à prendre en compte :
1. Les informations personnelles (état, blessures, équipement, …)
1. La cohérence RP (éductation, formation, …)
1. L'environnement perçu

Imaginons que l'on souhaite ajouter un bloc pour lancer une boule de feu.\
D'abord, je vais me focaliser sur Myself et faire les vérifications minimales, ce sont des conditions qui s'appliqueraient à tout le monde :
- Ai-je le sort en ma possession ?
    - `HaveSpell(WIZARD_FIREBALL)`
- Suis-je capacité de le lancer ?
    - `Global("BDAI_DISABLE_OFFENSIVE","LOCALS",0)`
    - `CheckStatLT(Myself, 50, SPELLFAILUREMAGE)`
    - `!StateCheck(Myself, STATE_SLEEPING)`
    - …

On y ajoute les informations RP, plus spécifiques au personnage :
    - `!AreaType(CITY)` (parce que le personnage respecte les lois)
    - `!Class(Myself, ICE_TROLL)` (parce qu'il craint le feu)

On intègre enfin les informations environnementales, qui dépendent des personnages autour :
    - `See(NearestEnemyOfType([0.0.TROLL]))`
    - `!Class(LastSeenBy(Myself, ELEMENTAL_FIRE))`
    - `!See([0.0.0.INNOCENT])`

