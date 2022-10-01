[![Build PlatformIO](https://github.com/btssn-lasalle84/github-actions-platformio/actions/workflows/build-platformio.yml/badge.svg?branch=main)](https://github.com/btssn-lasalle84/github-actions-platformio/actions/workflows/build-platformio.yml)

# github-actions-platformio

- [github-actions-platformio](#github-actions-platformio)
  - [PlatormIO et Visual Studio Code](#platormio-et-visual-studio-code)
    - [PlatformIO CLI](#platformio-cli)
    - [Créer un projet](#créer-un-projet)
    - [Éditer des fichiers](#éditer-des-fichiers)
    - [Fabriquer un projet](#fabriquer-un-projet)
    - [Programmer le système embarqué](#programmer-le-système-embarqué)
    - [Nettoyer un projet](#nettoyer-un-projet)
    - [Monitorer](#monitorer)
    - [Les bibliothèques](#les-bibliothèques)
  - [GitHub Actions](#github-actions)
    - [Principe de base](#principe-de-base)
    - [Exemple : Build PlatformIO](#exemple--build-platformio)
    - [Badge](#badge)

## PlatormIO et Visual Studio Code

[PlatormIO](https://platformio.org/) est un outil professionnel multi-plateforme, multi-architecture et multi-framework destiné aux ingénieurs en systèmes embarqués et aux développeurs de logiciels qui écrivent des applications pour ce type de système.

Liens :

- https://docs.platformio.org/en/latest/what-is-platformio.html
- http://tvaira.free.fr/dev/tutoriel/platformio.html
- http://tvaira.free.fr/dev/tutoriel/platformio-cli.html

[Visual Studio Code](https://code.visualstudio.com/) est un éditeur de code source extensible et disponible pour Windows, macOS et Linux.

Liens :

- https://code.visualstudio.com/docs
- https://platformio.org/install/ide?install=vscode
- https://docs.platformio.org/en/latest/integration/ide/vscode.html#quick-start
- http://tvaira.free.fr/projets/activites/activite-visualcode.html

### PlatformIO CLI

[PlatformIO CLI](https://docs.platformio.org/en/stable/core/index.html) fournit l'utilitaire `platformio` (ou son alias `pio`) pour programmer des systèmes embarqués (Arduino UNO, ESP32, etc.).

Dans un terminal :

```sh
$ platformio --version
PlatformIO Core, version 6.1.4

$ pio --version
PlatformIO Core, version 6.1.4

$ platformio --help
$ pio --help
```

_Remarque :_ `pio` est un alias de la commande `platformio`.

Guide : https://docs.platformio.org/en/stable/core/userguide/index.html

### Créer un projet

- Créer un répertoire pour le projet

```sh
$ mkdir -p nom-projet
$ cd nom-projet
```

- Initialiser le projet pour une carte et un _framework_

```sh
$ pio project init --board uno --project-option="framework=arduino"
...
include : Put project header files here
lib : Put here project specific (private) libraries
src : Put project source files here
platformio.ini : Project Configuration File
...
Project has been successfully initialized! Useful commands:
pio run : process/build project from the current directory
pio run --target upload or pio run -t upload : upload firmware to a target
pio run --target clean : clean project (remove compiled files)
...
```

- Voir l'état initial du projet

```sh
$ ls -l
drwxrwxr-x 2 tv tv 4096 sept. 27 07:59 include
drwxrwxr-x 2 tv tv 4096 sept. 27 07:59 lib
-rw-rw-r-- 1 tv tv  427 sept. 27 07:59 platformio.ini
drwxrwxr-x 2 tv tv 4096 sept. 27 07:59 src
drwxrwxr-x 2 tv tv 4096 sept. 27 07:59 test

$ cat platformio.ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino
```

### Éditer des fichiers

Les **fichiers sources** sont à placer dans `src/`, par exemple `src/main.cpp` pour un _framework_ Arduino :

```cpp
#include <Arduino.h>

void setup()
{
}

void loop()
{
}
```

### Fabriquer un projet


```sh
$ pio run -v
```

### Programmer le système embarqué

```sh
$ pio run -t upload -v
```

Les fichiers générés pendant la fabrication sont stockés dans un répertoire `.pio`.

:warning: Ces fichiers ne doivent jamais être conservés dans un dépôt `git`. Le plus simple est de "demander" à `git` de les ignorer. Pour cela, il suffit d'ajouter `.pio` dans le fichier `.gitignore` qui est situé à la racine du dépôt.

### Nettoyer un projet

Les fichiers générés à la fabrication peuvent être supprimés avec la commande suivante :

```sh
$ pio run --target clean
```

### Monitorer

```sh
$ platformio device list
$ platformio device monitor --help
```

Des informations spécifiques peuvent être conservées dans le fichier du projet `platformio.ini` :

```ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino

monitor_speed = 115200
```

### Les bibliothèques

La gestion des bibliothèques sous PlatormIO est contrôlée par la commande `pio pkg`

_Remarque :_ `pio lib` est considérée maintenant comme obsolète. Elle disparaîtra probablement dans les nouvelles versions.

Les bibliothèques peuvent être installées :

- globalement (dans le dossier d'installation de PlatformIO)

```sh
$ pio lib -g list
$ pio pkg list --global --only-libraries
...
```

- localement (dans le dossier du projet)

```sh
$ pio lib list
$ pio pkg list --only-libraries
```

Et de manière générale : `pio pkg list`

Les commandes utiles :

- rechercher une bibliothèque :

```sh
$ pio pkg search xxx
```

- voir les informations sur une bibliothèque :

```sh
$ pio pkg show xxx
```

- installer une bibliothèque (avec l'option `--global` pour l'installer globalement) :

```sh
$ pio pkg install xxx
```

Des informations spécifiques sur les bibliothèques peuvent être conservées avec `lib_deps` dans le fichier du projet `platformio.ini`, par exemple :

```ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino

lib_deps =
  fastled/FastLED @ ^3.4.0
```

_Remarque :_ Il est possible de créer ses propres bibliothèques à l’intérieur du projet dans le dossier `lib`. Le principe est de créer un sous-répertoire qui porte le même nom que le nom des fichiers sources (`.h` et `.cpp`). Il est aussi possible de placer des fichiers d’entête (`.h`) dans le dossier `include` du projet.

## GitHub Actions

GitHub Actions permet d'automatiser des tâches associées à un dépôt GitHub.

La "tâche" (le travail) est décrite dans un fichier `.yml` stocké dans l'arborescence `.github/workflows/`.

Le fichier peut être créé directement à partir de l'interface web de GitHub dans l'onglet `Actions` puis `New workflow`.

GitHub Actions propose alors des modèles prêts à l'emploi. On peut créer son propre _workflow_ en cliquant sur `set up a workflow yourself`. Un squelette est alors fourni.

### Principe de base

Il faut commencer par donner un nom au fichier, par exemple : `build-platformio.yml`

Le _workflow_ doit avoir un **nom** :

```yml
name: Build PlatformIO
```

Puis on définit l'évènement (_on_) déclencheur (_trigger_), ici un `git push` sur la branche principale `main` :

```yml
on:
  push:
    branches: [main]
```

Ensuite, on décrit le travail (_job_) à exécuter (_runs-on_) sur une machine (virtuelle, par exemple Ubuntu), qui est généralement composé d'étapes (_step_) :

- Extraction du dépôt
- Installation et configuration de PlatformIO
- Fabrication du projet PlatformIO

```yml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # Checkout repository
      - name: Checkout code
        uses: actions/checkout@v2

      # Setup PlatformIO in PATH
      - name: Setup PlatformIO
        uses: n-vr/setup-platformio-action@v1

      # Build the PlatformIO project
      - name: Build PlatformIO project
        run: pio run
```

_Remarque :_ Le mot clé `uses` permet de récupérer des actions existantes fournies par des contributeurs.

### Exemple : Build PlatformIO

Le fichier `.github/workflows/build-platformio.yml` complet :

```yml
name: Build PlatformIO
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # Checkout repository
      - name: Checkout code
        uses: actions/checkout@v2

      # Setup PlatformIO in PATH
      - name: Setup PlatformIO
        uses: n-vr/setup-platformio-action@v1

      # Build the PlatformIO project
      - name: Build PlatformIO project
        run: pio run
```

### Badge

Il est possible d'associer un badge d'état (une image) à un _workflow_.

Pour cela, il faut aller dans l'onglet `Actions` puis cliquer sur le _workflow_ (dans la liste à gauche). Il faut ensuite cliquer sur les trois petits points `...` (à droite de la zone de recherche) et sélectionner `Create status badge`. Une boîte de dialogue s'affiche et il faut récupérer le code Markdown (`Copy status badge Mardown`).

Ce code est à placer dans le fichier `README.md` du dépôt (généralement au tout début du fichier) pour afficher le badge sur la page d'accueil.

```markdown
[![Build PlatformIO](https://github.com/<organisation>/<depot>/actions/workflows/build-platformio.yml/badge.svg)](https://github.com/<organisation>/<depot>/actions/workflows/build-platformio.yml)
```

---
©️ LaSalle Avignon - 2022 - <thierry.vaira@gmail.com>
