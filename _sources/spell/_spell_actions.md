---
myst:
  substitutions:
    OBJ_FILENAME: FILENAME
    SPELL: SPELL_NAME
    TARGET: LastSeenBy(Myself)
---

# Actions Spell


Parfois, on a besoin de s'affranchir des contraintes habituelles de l'action `Spell`.\
D'autres actions existent, encore faut-il connaître leurs propres contraintes.

Voici un petit tableau qui récapitule les différences entres les actions principales.\
Il est parfois possible d'ajouter des contraintes pour "simuler" un effet et parfois non.\
Cela permet de mieux choisir l'action et les triggers qui vous permettrons de répondre au mieux au besoin.

**Légende** :
- ✅ : l'effet est géré et vous ne pouvez pas vous y soustraire
  - Si vous ne voulez pas de cet effet, regardez les actions plus permissives (colonne de droite)
- ❌ : l'effet n'est pas géré et il n'y a pas de solution connue pour le simuler
  - Si vous voulez cet effet, regardez les actions moins permissives (colonne de gauche)
- Autre : l'effet n'est pas géré par défaut mais l'action indiquée peut le simuler
  - Si vous voulez de cet effet, ajoutez le trigger indiqué dans votre bloc `IF`


`````{tab-set}
````{tab-item} Comparatif
| Effet | Spell | ForceSpell | ReallyForceSpell |
| - | - | - | - |
| Sort possédé | ✅$^($$^1$$^)$ | HaveSpell | HaveSpell |
| Retrait du sort | ✅$^($$^1$$^)$ | RemoveSpell | RemoveSpell |
| Temps d'incantation | ✅ | ✅ | Wait$^($$^2$$^)$ |
| Effet incantatoire | ✅ | ✅ | ❌ |
| Round d'attente entre action | ✅ | SetGlobalTimer | SetGlobalTimer |
| Contrainte de portée | ✅ | ForceSpellRange | ❌ |
| Contrainte de silence | ✅ | HaveSpell | HaveSpell |
| Contrainte de OUTDOOR | ✅ | AreaType$^($$^2$$^)$ | AreaType$^($$^2$$^)$ |
| Contrainte de NonCombat | ✅ | ActuallyInCombat$^($$^2$$^)$ | ActuallyInCombat$^($$^2$$^)$ |
| Contrainte sur la concentration | ✅ | ❌ | ❌ |
| Contrainte sur l'échec incantation | ✅ | ❌ | ❌ |
| Contrainte ciblage de l'invisible | ✅ | See | See |
| Affecté par les hiatus | ✅ | ✅ | ❌ |
| Gestion bonus niveau entropiste | ✅ | ✅ | ❌ |
| Impact de l'hostilité sur l'invisibilité | ✅ | ✅ | ❌$^($$^3$$^)$ |
| Activation des triggers SpellCast | ✅ | ✅ | ❌ |
| Log de combat | ✅$^($$^4$$^)$ | ✅$^($$^4$$^)$ | ✅$^($$^4$$^)$ |
| Incantation si STATE_DEAD | ❌ | ❌ | ReallyForceSpellDead |

$^($$^1$$^)$ : possible de supprimer cette contrainte en utilisant `SpellNoDec`\
$^($$^2$$^)$ : solution non dynamique\
$^($$^3$$^)$ : si le sort est hostile, il est possible de rajouter un `ApplySpell` sur le jeteur de sort qui contiendrait les opcodes [136](https://gibberlings3.github.io/iesdp/opcodes/bgee.htm#op136) et [160](https://gibberlings3.github.io/iesdp/opcodes/bgee.htm#op160), solution non dynamique\
$^($$^4$$^)$ : seulement si la capacité à une StrRef
````
`````


