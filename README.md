# Qué es Git flow y cómo funciona

Gitflow es un flujo de trabajo de Git heredado que fue en origen a una estrategia innovadora y revolucionaria para gestionar ramas de Git.

Gitflow es un modelo alternativo de creación de ramas en Git en el que se utilizan ramas de función y varias ramas principales.

## Funcionamiento

![Funcionamiento](https://github.com/JorgeGordilloDev/gitflow/blob/master/doc/01.svg)

### Ramas principales y de desarrollo

En lugar de una única rama `master`, este flujo de trabajo utiliza dos ramas para registrar el historial del proyecto. La rama `master` almacena el historial de publicación oficial y la rama `dev` sirve como rama de integración para las funciones. Asimismo, conviene etiquetar todas las confirmaciones de la rama `main` con un número de versión.

El primer paso es complementar la rama `main` predeterminada con una rama `dev`.

#### Creacion de rama dev

```cmd
$ git checkout -b dev master
$ git push origin dev
```

Esta rama contendrá el historial completo del proyecto, mientras que main contendrá una versión abreviada. A continuación, otros desarrolladores deberían clonar el repositorio central y crear una rama de seguimiento para dev.

### Ramas de apoyo

![Ramas de apoyo](https://github.com/JorgeGordilloDev/gitflow/blob/master/doc/04.svg)

Junto a las ramas `master` y `dev`, existe un conjunto de ramas de apoyo y su objetivo es el permitir el desarrollo en paralelo entre los miembros del equipo, la resolución de problemas en producción de forma rápida, etc. A diferencia de las ramas principales, estas están limitadas en tiempo. Serán eliminadas eventualmente. Los diferentes tipos de ramas que se usarán son:

1. feature
2. release
3. hotfix

### Ramas de feacture

Todas las caracteristicas nuevas deben residir en su propia rama, que se puede enviar al repositorio central para copia de seguridad/colaboración. Sin embargo, en lugar de ramificarse de `main`, las ramas feature utilizan la rama `dev` como rama primaria. Cuando una función está terminada, se vuelve a fusionar en `dev`. Las funciones no deben interactuar nunca directamente con `main`.

![Feacture](https://github.com/JorgeGordilloDev/gitflow/blob/master/doc/02.svg)

Ten en cuenta que las ramas `feature` combinadas con la rama `dev` conforman, a todos efectos, el flujo de trabajo de ramas de función. Sin embargo, el flujo de trabajo Gitflow no termina aquí.

Las ramas `feature` suelen crearse a partir de la última rama `dev`.

#### Creacion de rama feature

```cmd
coding...
git add .
git commit -a -m "add dev page"
git push --set-upstream origin feature/...
coding...
git add .
git commit -a -m "feat: update dev page"
git push

```

### Branch Feature

START

```cmd
git checkout -b feature/... dev
coding...
git add .
git commit -a -m "feat: add login page"
git push --set-upstream origin feature/...
coding...
git add .
git commit -a -m "feat: update login page"
git push
```

FINISH

```cmd
$ git checkout dev
$ git merge --no-ff feature/...
$ git branch -d feature/...
$ git push origin develop

or create a pull request
```

### Branch Release

START

```cmd
$ git checkout -b release/... dev
coding
$ git add .
$ git commit -a -m "Release version 1.1.0 of the project"
```

FINISH

```cmd
$ git checkout master
$ git merge --no-ff release/...
$ git tag -a 1.0
$ git push origin master

$ git checkout dev
$ git merge --no-ff release/...
$ git push origin dev

$ git branch -d release/...
```

### Branch Release

START

```cmd
$ git checkout -b hotfix/1.1.1 master
coding
$ git add .
$ git commit -a -m "Bumped version number to 1.1.1"
```

FINISH

```cmd
$ git checkout master
$ git merge --no-ff hotfix/1.1.1
$ git tag -a 1.1.1

$ git checkout dev
$ git merge --no-ff hotfix/1.1.1

git branch -d hotfix/1.1.1
```
