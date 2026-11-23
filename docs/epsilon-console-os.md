# Installer un OS console sur une NumWorks

> **Ce projet vise le firmware Epsilon complet**, pas un thème NumPlay exporté en
> `.nwa`. NumPlay Studio et les jeux `.nwa` restent des éléments séparés du
> système d'exploitation.

## Ce que tu vas obtenir

L'exemple de départ est **Switch-OS** : un accueil inspiré de la Nintendo Switch,
avec les applications natives de la calculatrice en raccourcis et les jeux
`.nwa` dans leur propre rangée. Le firmware est compilé pour la calculatrice ;
il est ensuite installé dans le second emplacement système (slot B), sans
remplacer le système officiel du slot A.

Le but du projet NumPlay est de garder les fonctions utiles de la calculatrice
au premier plan : Calculs, Grapheur, Équations, Statistiques, matrices, Python,
Paramètres et applications. Les jeux restent une catégorie à part. Une icône
visible ne suffit toutefois pas à prouver qu'une fonction marche : les tests
matrices/images/Python décrits plus bas sont obligatoires.

## 1. Choisir le projet de départ

Les projets étudiés sont des firmwares Epsilon alternatifs. Leurs patchs
modifient l'accueil et ne doivent pas être appliqués les uns sur les autres.

| Projet | Style |
| --- | --- |
| [Switch-OS](https://github.com/derim00/switch-os) | Grandes vignettes de jeux et raccourcis d'applications |
| [PSP-OS](https://github.com/derim00/psp-os) | Catégories horizontales et liste verticale |
| [Wii U-OS](https://github.com/derim00/wiiu-os) | Pages de tuiles |
| [3DS-OS](https://github.com/derim00/3ds-os) | Carrousel et interface à deux zones |

On commence par Switch-OS comme base de travail. Si un autre style est retenu,
il faut utiliser son patch à la place, pas ajouter les quatre patchs ensemble.

Les quatre projets ciblent le commit Epsilon
`ea4d93bac83ccdf2ab7bc562e994408f3be11d60`. Le Makefile de cette base annonce
`26.1.0`, tandis que les patchs règlent `APP_VERSION` sur `26.4.0`.
Cette modification de numéro ne signifie pas que tout le code interne d'une
version ultérieure a été intégré. Il faut conserver la base exacte et tester
le résultat.

## 2. Préparer une copie privée pour compiler le firmware

Le dépôt Switch-OS explique que le firmware Epsilon n'est pas redistribué
depuis son dépôt public. Sa compilation se fait dans une copie **privée** du
projet, dans ton compte GitHub.

1. Ouvre [derim00/switch-os](https://github.com/derim00/switch-os).
2. Utilise **Use this template**, puis **Create a new repository**.
3. Donne un nom à ta copie et règle sa visibilité sur **Private**.
4. Dans cette copie privée, ouvre **Settings** puis vérifie que GitHub Actions
   est autorisé à exécuter les workflows.
5. Ouvre l'onglet **Actions** et vérifie que le workflow **Build Switch-OS**
   est disponible.

Ne mets pas le code source Epsilon ni les firmwares compilés dans le dépôt
public NumPlay. Le dépôt public NumPlay peut conserver ce guide, les
configurations et les liens vers les sources, mais la compilation du firmware
reste dans le dépôt privé prévu à cet effet.

## 3. Ajouter les applications et les jeux

Il faut distinguer deux familles :

- **Applications natives Epsilon** : Calculs, Grapheur, Équations,
  Statistiques, Python, Paramètres, etc. Elles font partie du firmware et
  Switch-OS les présente dans ses raccourcis. On ne les ajoute pas comme des
  jeux.
- **Applications NumWorks au format `.nwa`** : elles sont ajoutées dans le
  dossier `games/` de ta copie privée Switch-OS. Le workflow les prépare pour
  le slot B et les inclut dans le paquet d'installation.

Pour ajouter un jeu ou une application `.nwa` :

1. Ouvre le dossier `games/` de ta copie privée.
2. Choisis **Add file → Upload files**.
3. Dépose le fichier `.nwa` voulu et valide le commit.
4. Vérifie que le fichier est bien présent dans `games/` avant de lancer le
   build.

La place disponible dans le slot B est limitée. Le projet indique qu'un fichier
trop volumineux peut être ignoré par la préparation. Il faut donc vérifier les
messages du build et ne pas considérer un fichier comme installé simplement
parce qu'il a été téléversé dans GitHub.

## 4. Compiler le firmware et les installateurs

Dans ta copie privée :

1. Va dans **Actions**.
2. Sélectionne **Build Switch-OS**.
3. Clique sur **Run workflow** et confirme.
4. Attends la fin des tâches de compilation.
5. Ouvre le résultat du workflow et télécharge l'artefact adapté à ton
   ordinateur : **Switch-OS-Mac** ou **Switch-OS-Windows**.

Le workflow construit un firmware pour chaque modèle pris en charge
(`n0110`, `n0115`, `n0120`) et prépare les installateurs. Il faut attendre
que le workflow soit terminé avec succès et que les deux artefacts soient
disponibles avant de passer à l'installation.

### Compilation locale sur Mac (alternative)

Le projet fournit aussi un script local. D'après son README, les prérequis et
commandes sont :

```bash
brew install make dfu-util libusb
brew install --cask gcc-arm-embedded
git clone https://github.com/derim00/switch-os.git
cd switch-os
./scripts/build-firmware.sh
```

La voie GitHub Actions privée est préférable si tu ne veux pas installer la
chaîne de compilation sur ton ordinateur.

## 5. Installer sur la calculatrice

**Avant de commencer :** vérifie le modèle exact de la calculatrice et la
version officielle. Le README Switch-OS indique les modèles n0110, n0115 et
n0120 et demande que le système officiel du slot A soit en version **26.4.0**.
Ne sélectionne jamais un firmware destiné à un autre modèle.

### Windows

1. Décompresse entièrement l'artefact **Switch-OS-Windows**.
2. Lance `Installer Switch-OS.exe` depuis le dossier extrait.
3. Branche la calculatrice en USB.
4. Suis les instructions de la page locale de l'installateur.
5. Laisse l'installateur détecter le modèle et vérifier le slot disponible.
6. Lance l'installation seulement si le modèle et les vérifications affichées
   sont corrects.

### macOS

1. Décompresse l'artefact **Switch-OS-Mac** (le README signale qu'il peut
   contenir une archive ZIP à extraire une seconde fois).
2. Lance `Installer Switch-OS.command` depuis le dossier extrait.
3. Branche la calculatrice en USB.
4. Suis les instructions de la page locale de l'installateur.
5. Vérifie le modèle détecté et le slot cible avant de confirmer.

Selon le README de Switch-OS, l'installateur utilise le slot B et laisse le
système officiel dans le slot A. Après un redémarrage matériel (Reset), la
calculatrice peut revenir sur le système officiel ; le système alternatif
reste dans le slot B et peut être relancé via son installateur. Ne force pas
une installation si le slot cible n'est pas libre ou si l'outil signale une
incompatibilité.

## 6. Vérifier les applications après installation

Après le premier démarrage, vérifie séparément les raccourcis natifs et les
applications `.nwa` ajoutées. Le menu doit rester utilisable au clavier :
navigation, validation avec OK/EXE, retour à l'accueil et conservation de la
sélection.

Checklist de validation :

- [ ] Calculs s'ouvre et permet d'effectuer un calcul.
- [ ] Grapheur s'ouvre et affiche une fonction.
- [ ] Équations et Statistiques restent accessibles.
- [ ] Les matrices peuvent être créées, modifiées et réutilisées.
- [ ] Python s'ouvre et les programmes existants sont présents.
- [ ] Un programme Python peut lire et utiliser les données matricielles
      nécessaires au scénario visé.
- [ ] Le parcours précis **image → matrice → programme Python** est testé avec
      un exemple reproductible.
- [ ] Paramètres et informations de la calculatrice restent accessibles.
- [ ] Chaque application `.nwa` ajoutée se lance et revient correctement à
      l'accueil.
- [ ] Le redémarrage et le retour au système officiel fonctionnent.

Les matrices et leurs résultats supplémentaires sont gérés dans l'application
Calculs d'Epsilon ; Python se trouve dans l'application Code. L'inspection des
sources ne suffit pas à confirmer le parcours image/matrice/Python : il faut
effectuer le test réel sur le firmware installé. Ne marque pas cette fonction
comme validée avant d'avoir observé le résultat.

## 7. Organisation prévue avec NumPlay

NumPlay reste le projet d'applications et de jeux `.nwa`. Le firmware console
est un chantier Epsilon séparé. À terme, NumPlay Studio pourra proposer un lien
vers le guide et les choix d'interface, mais il ne devra pas présenter un
fichier `.nwa` comme s'il s'agissait du système complet.

Le dépôt `tomcalif9-stack/NumPlay` conserve les changements sur la branche
`feature/console-themes`. La branche `main` ne doit pas être modifiée pour
ce chantier.

## Références techniques

- [Switch-OS : instructions officielles du projet](https://github.com/derim00/switch-os)
- [Workflow de compilation Switch-OS](https://github.com/derim00/switch-os/blob/main/.github/workflows/build.yml)
- [Patch Switch-OS pour Epsilon](https://github.com/derim00/switch-os/blob/main/patches/switch-os-epsilon.patch)
- [Sources Epsilon à la base des patchs](https://github.com/numworks/epsilon/tree/ea4d93bac83ccdf2ab7bc562e994408f3be11d60)
- [PSP-OS](https://github.com/derim00/psp-os), [Wii U-OS](https://github.com/derim00/wiiu-os), [3DS-OS](https://github.com/derim00/3ds-os)
