# Projet : interfaces console pour Epsilon

> État : cadrage technique. Ce document distingue les thèmes visuels de NumPlay des
> véritables interfaces d'accueil du firmware Epsilon.

## Objectif

Proposer depuis NumPlay Studio une interface d'accueil inspirée de consoles, puis
produire un firmware Epsilon adapté. La calculatrice doit rester pleinement utile
pour les mathématiques et la programmation : l'interface console ne doit jamais
transformer l'appareil en simple lanceur de jeux.

## Deux projets différents

- **Thèmes NumPlay** : habillage de l'application NumPlay (.nwa). Ils ne changent
  pas l'accueil général de la calculatrice.
- **Interfaces Epsilon** : modifications du firmware, notamment de
  `apps/home`, du contrôleur de navigation et de certains composants Escher.
  Elles remplacent réellement l'accueil et demandent une compilation Epsilon.

Le Makefile de NumPlay construit NumPlay et les jeux en `.nwa`. Le workflow
`.github/workflows/build.yml` publie le kit utilisé par NumPlay Studio. Ce
workflow ne compile pas Epsilon et ne peut donc pas, à lui seul, produire un
système d'exploitation console.

## Références étudiées

Les quatre dépôts de référence sont :

| Projet | Interface de référence | Commit Epsilon ciblé |
| --- | --- | --- |
| Switch-OS | Grandes vignettes et rangée d'applications | `ea4d93bac83ccdf2ab7bc562e994408f3be11d60` |
| PSP-OS | Catégories horizontales et applications verticales | Même commit |
| Wii U-OS | Pages de tuiles et barre de raccourcis | Même commit |
| 3DS-OS | Carrousel et présentation à deux zones | Même commit |

Ces patchs sont des alternatives : ils touchent tous le contrôleur et l'écran
d'accueil d'Epsilon. Ils ne doivent pas être appliqués les uns sur les autres
sans travail de fusion et de conception.

Les interfaces PlayStation 5, Xbox et Wii n'ont pas encore de base Epsilon
équivalente validée dans cette étude. Elles devront être développées séparément,
sans supposer qu'un thème CSS de NumPlay suffit à modifier le firmware.

## Règles fonctionnelles non négociables

L'accueil doit présenter les fonctions de calcul au même niveau de soin que les
jeux. Il faut garder des accès évidents vers :

- Calculs et calcul formel ;
- Grapheur ;
- Équations, statistiques et probabilités ;
- Matrices et les outils qui permettent de les créer, modifier et réutiliser ;
- Python et les programmes installés ;
- Paramètres et informations de la calculatrice ;
- Jeux et applications NumPlay.

### Images et matrices

Ne pas supprimer ni remplacer les applications natives de matrices. Les parcours
qui permettent de stocker une image dans une matrice puis de la récupérer ou de la
traiter depuis un programme doivent rester possibles. Les tests devront vérifier
le parcours réel de bout en bout sur le firmware modifié, pas seulement la présence
d'une icône Matrices dans le menu.

### Navigation

- Chaque catégorie doit être accessible au clavier de la calculatrice.
- OK/EXE lance l'application sélectionnée.
- Le retour depuis une application ramène à l'accueil sans perdre la sélection.
- Les raccourcis vers Calculs, Matrices, Python et Paramètres ne doivent pas être
  cachés derrière la catégorie Jeux.
- Les libellés et icônes doivent être compréhensibles même sans connaître la
  console dont le style s'inspire.

## Architecture cible

1. **Studio** : choix explicite de l'interface Epsilon, séparé du thème visuel de
   l'application NumPlay.
2. **Configuration de build** : associe chaque interface à une source et à un
   ensemble de modifications identifiés et versionnés.
3. **Construction Epsilon** : récupère le commit Epsilon autorisé, applique une
   seule implémentation d'accueil, puis compile pour le modèle demandé.
4. **Validation** : compilation, simulateur, tests de navigation, puis vérification
   des applications natives et des parcours matrices/images/Python.
5. **Installation** : étape séparée, clairement avertie et validée pour le modèle
   exact de calculatrice. Ne jamais écraser le système officiel par défaut.

## Compatibilité et validation

Les quatre projets de référence ciblent le même commit Epsilon, mais cela ne
garantit pas leur compatibilité avec les versions futures. Le commit devra être
épinglé et les mises à jour devront passer par une vérification des patchs.

Matrice de tests minimale pour chaque interface :

- démarrage et retour à l'accueil ;
- ouverture et fermeture de chaque application native ;
- Calculs, Grapheur, Équations et Statistiques ;
- création/réutilisation de matrices et parcours image-vers-matrice ;
- exécution d'un programme Python utilisant les données concernées ;
- lancement d'une application NumPlay et retour à l'accueil ;
- navigation complète au clavier et affichage dans le simulateur ;
- compilation distincte pour n0110, n0115 et n0120, si ces modèles sont pris en charge.

Une interface ne doit pas être déclarée terminée si seuls les aperçus graphiques
ou la compilation ont été vérifiés.

## Licences et distribution

Les dépôts Switch-OS, PSP-OS, Wii U-OS et 3DS-OS indiquent CC BY-NC-SA 4.0.
Le dépôt NumPlay est sous GPL-3.0. Il ne faut pas incorporer directement leurs
patchs dans NumPlay ni redistribuer un assemblage sous GPL sans avoir résolu
explicitement la compatibilité des licences et obtenu les autorisations nécessaires.

La source Epsilon et les droits de NumWorks sont distincts de ces projets. Le
dépôt NumPlay public ne doit pas recevoir de source ou de firmware Epsilon dont
la redistribution n'est pas autorisée. Une intégration des patchs existants
nécessite l'accord des auteurs ; une autre voie est une implémentation originale
qui ne copie pas leur code ni leurs dessins.

## État réel au démarrage du chantier

- [x] Dépôt NumPlay et branche `feature/console-themes` identifiés.
- [x] Différence entre application NumPlay et firmware Epsilon confirmée.
- [x] Quatre projets de référence et leur commit Epsilon commun identifiés.
- [x] Fonctions natives à préserver définies.
- [ ] Compatibilité de chaque patch testée sur sa base Epsilon.
- [ ] Autorisations de réutilisation clarifiées, ou interfaces originales retenues.
- [ ] Prototype Epsilon avec accueil console et catégories calculatrice.
- [ ] Tests réels matrices/images/Python réussis.
- [ ] Sélecteur Studio relié à une vraie chaîne de build firmware.
