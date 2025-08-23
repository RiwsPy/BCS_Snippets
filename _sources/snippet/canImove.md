# ⚠️ Can I Move

Snippet incomplet qui permet de savoir si le personnage peut se déplacer.\
Aussi bizarre que cela puisse paraître, il n'existe pas de solution efficace pour connaître sa vitesse de déplacement.

```cr
IF
    //# …

    !StateCheck(Myself, STATE_HELPLESS | STATE_SLEEPING | STATE_REALLY_DEAD | STATE_FEEBLEMINDED)
    CheckStat(Myself, 0, CASTERHOLD)
    !CheckStat(Myself, 2, ENCUMBERANCE)
    !CheckSpellState(Myself, DEFENSIVE_SPIN) //# non fiable

    //# …
THEN
```

```{note}
Lorsqu'on lance un sort, on vérifie rarement la distance entre la cible et le lanceur.\
Or si la distance entre les deux personnes est supérieure à la portée du sort et que le lanceur ne peut se déplacer... cela va bloquer le script.
```


## STATE_FEEBLEMINDED

La gestion de la débilité mentale est discutable selon les cas d'usage.\
En effet, cet état n'empêche pas le mouvement en tant que tel, le personnage :
- n'est plus sélectionnable par le joueur
- n'exécute plus ces scripts

Cependant, il n'empêche pas les interventions extérieures : `ActionOverride(LastSeenBy(Myself), MoveToObject(Player1))` fonctionnera sur les personnages malgré un STATE_FEEBLEMINDED actif.\
Ce n'est pas le cas des autres contraintes.
