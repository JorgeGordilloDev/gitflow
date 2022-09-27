# Qué es Git flow y cómo funciona

Gitflow es un flujo de trabajo de Git heredado que fue en origen a una estrategia innovadora y revolucionaria para gestionar ramas de Git.

Gitflow es un modelo alternativo de creación de ramas en Git en el que se utilizan ramas de función y varias ramas principales.

## Funcionamiento

![Funcionamiento](https://raw.githubusercontent.com/JorgeGordilloDev/gitflow/478fcf5a351f4894a4204fd30f2517f6b45948ba/doc/01.svg)

### Ramas principales y de desarrollo

En lugar de una única rama `master`, este flujo de trabajo utiliza dos ramas para registrar el historial del proyecto. La rama `master` almacena el historial de publicación oficial y la rama `dev` sirve como rama de integración para las funciones. Asimismo, conviene etiquetar todas las confirmaciones de la rama `main` con un número de versión.

El primer paso es complementar la rama `main` predeterminada con una rama `dev`.

### Creacion de rama dev

```ps
$ git checkout -b dev master
$ git push --set-upstream origin dev
```

Esta rama contendrá el historial completo del proyecto, mientras que main contendrá una versión abreviada. A continuación, otros desarrolladores deberían clonar el repositorio central y crear una rama de seguimiento para dev.

## Ramas de apoyo

![Ramas de apoyo](https://raw.githubusercontent.com/JorgeGordilloDev/gitflow/478fcf5a351f4894a4204fd30f2517f6b45948ba/doc/04.svg)

Junto a las ramas `master` y `dev`, existe un conjunto de ramas de apoyo y su objetivo es el permitir el desarrollo en paralelo entre los miembros del equipo, la resolución de problemas en producción de forma rápida, etc. A diferencia de las ramas principales, estas están limitadas en tiempo. Serán eliminadas eventualmente. Los diferentes tipos de ramas que se usarán son:

1. feature
2. release
3. hotfix

## Ramas de caracteristicas

Todas las caracteristicas nuevas deben residir en su propia rama, que se puede enviar al repositorio central para copia de seguridad/colaboración. Sin embargo, en lugar de ramificarse de `main`, las ramas feature utilizan la rama `dev` como rama primaria. Cuando una caracteristica está terminada, se vuelve a fusionar en `dev`. Las features no deben interactuar nunca directamente con `main`.

![Feacture](https://raw.githubusercontent.com/JorgeGordilloDev/gitflow/478fcf5a351f4894a4204fd30f2517f6b45948ba/doc/02.svg)

Ten en cuenta que las ramas `feature` combinadas con la rama `dev` conforman, a todos efectos, el flujo de trabajo de ramas de caracteristicas. Sin embargo, el flujo de trabajo Gitflow no termina aquí.

Las ramas `feature` suelen crearse a partir de la última rama `dev`.

### Creacion de rama Feature

```ps
$ git checkout -b feature/example dev
$ git push --set-upstream origin feature/example

(Hacer las modificaciones necesarias)
$ git add .
$ git commit -a -m "feat: add dev page" -m "context..."
$ git push
```

### Finalizacion de rama Feature

Cuando hayas terminado con el trabajo de desarrollo en la caracteristica, el siguiente paso es crear un pull request para que revisen el codigo o directamente fusionar feature_branch en dev.

```ps
create a pull request or

$ git checkout dev
$ git merge --no-ff feature/example
(Resolver conflictos)
$ git push origin dev
```

El indicador --no-ff hace que la combinación siempre cree un nuevo objeto de confirmación. Esto evita perder información sobre la existencia histórica de una rama feacture y agrupa todas las confirmaciones que agregaron la caracteristica.

Una vez integrada la rama en `dev` eliminaremos la rama en el repositorio local.

```ps
$ git branch -d feature/example
```

Y por ultimo eliminamo la rama remota.

```ps
$ git push origin :feature/example
```

## Ramas de publicacion

![Feacture](https://raw.githubusercontent.com/JorgeGordilloDev/gitflow/478fcf5a351f4894a4204fd30f2517f6b45948ba/doc/03.svg)

Cuando `dev` haya adquirido suficientes funciones para una publicación (o se acerque una fecha de publicación predeterminada), debes bifurcar una rama release (o de publicación) a partir de `dev`. Al crear esta rama, se inicia el siguiente ciclo de publicación, por lo que no pueden añadirse nuevas funciones una vez pasado este punto (en esta rama solo deben producirse las soluciones de errores, la generación de documentación y otras tareas orientadas a la publicación). Cuando está lista para el lanzamiento, la rama release se fusiona en `master` y se etiqueta con un número de versión. Además, debería volver a fusionarse en `dev`, ya que esta podría haber progresado desde que se iniciara la publicación.

Crear ramas release es otra operación de ramificación sencilla. Al igual que las ramas `feature`, las ramas `release` se basan en la rama `dev`. Se puede crear una nueva rama `release` utilizando los siguientes métodos.

### Creacion de rama Release

```ps
$ git checkout -b release/1.2.0 dev
$ git push --set-upstream origin release/1.2.0

(Hacer las modificaciones necesarias)
$ git add .
$ git commit -a -m "Release version 1.2.0 of the project" -m "context..."
$ git push
```

### Finalizacion de rama Release

Cuando hayas terminado con el trabajo de la publicacion, el siguiente paso es crear un pull request para que revisen el codigo o directamente fusionar release_branch en `master` y en `dev`.

Primero debemos actualizar la rama `master`.

```ps
crear pull request o

$ git checkout master
$ git merge --no-ff release/1.2.0
$ git tag -a 1.2.0
$ git push origin master
```

A continuación, debemos guardar esos cambios en la rama `dev`.

```ps
crear pull request o

$ git checkout dev
$ git merge --no-ff release/1.2.0
$ git push origin dev
```

Una vez integrada la rama tanto en `master` como en `dev` eliminaremos la rama local en el repositorio local.

```ps
$ git branch -d release/1.2.0
```

Y por ultimo eliminamo la rama remota.

```ps
$ git push origin :release/1.2.0
```

## Ramas de corrección

![Feacture](https://raw.githubusercontent.com/JorgeGordilloDev/gitflow/478fcf5a351f4894a4204fd30f2517f6b45948ba/doc/04.svg)

Las ramas de mantenimiento, de corrección o de `hotfix` sirven para reparar rápidamente las publicaciones de producción. Las ramas `hotfix` son muy similares a las ramas `release` y `feature`, salvo por el hecho de que se basan en la rama `master` y no en la `dev`. Esta es la única rama que debería bifurcarse directamente a partir de `master`. Cuando se haya terminado de aplicar la corrección, debería fusionarse en `master` y `dev` (o la rama release actual), y `master` debería etiquetarse con un número de versión actualizado.

### Creacion de rama Hotfix

```ps
$ git checkout -b hotfix/1.2.1 master
$ git push --set-upstream origin hotfix/1.2.1

(Hacer los cambios necesarios)
$ git add .
$ git commit -a -m "fix(front): Problema de producción grave solucionado" -m "context"
$ git push
```

### Finalizacion de rama Hotfix

Al igual que al finalizar una rama `release`, una rama `hotfix` se fusiona tanto en `master` como en `dev` (o la rama `release` actual).

Primero debemos actualizar la rama master y etiquetarla.

```ps
crear un pull request o

$ git checkout master
$ git merge --no-ff hotfix/1.2.1
$ git tag -a 1.2.1
```

A continuación debemos incluir el hotfix en `dev` también.

```ps
crear un pull request o

$ git checkout dev
$ git merge --no-ff hotfix/1.2.1
```

Una vez integrada la rama tanto en `master` como en `dev` eliminaremos la rama en el repositorio local.

```ps
$ git branch -d hotfix/1.2.1
```

Y por ultimo eliminamo la rama remota.

```ps
$ git push origin :hotfix/1.2.1
```

## Buenas practicas en los commit

Escribir buenos mensajes de commit es importante para que el histórico de tu proyecto sea legible, fácilmente escaneable y, claro, entendible por cualquier persona que participe en el proyecto.

### 1. Usa el verbo imperativo (`Add`, `Change`, `Fix`, `Remove`, …)

Aunque el mensaje puede sonar un poco borde, el verbo presente es una forma de expresar la acción que se realiza en el commit. Por ejemplo, `Add` significa que se añade un nuevo archivo, `Change` significa que se modifica un archivo existente y `Fix` significa que se arregla un bug.

Sé que muchas veces estamos tentados a escribirlo en pasado “Added…”, “Fixed…” o “Removed…” pero cada commit hay que entenderlo como una instrucción para cambiar el estado del proyecto. Dicho de otro modo, el verbo presente nos permite saber qué estado queremos que el proyecto se encuentre en el momento de añadir el commit.

Lo mejor es que el mensaje del commit complete esta frase: “Si aplico este commit, entonces este commit…”

1. …add a new search feature
2. …fix a problem with the topbar
3. …change the default system color
4. …remove a random notification

### 2. No uses punto final ni puntos suspensivos en tus mensajes

Usar puntuación, más allá de las comas, es innecesario a la hora de crear un buen mensaje de commit. Cada carácter cuenta a la hora de crear un buen mensaje de commit así que no lo desperdicies con puntos innecesarios.

```ps
$ git commit -a -m "Add new search feature." # ❌
$ git commit -a -m "Fix a problem with the topbar..." # ❌
$ git commit -a -m "Change the default system color" # ✅
```

¿Por qué? **El primer mensaje de commit es el título del commit**. Y según las reglas de puntuación para títulos, tanto en castellano como en inglés, estos no llevan puntuación final.

### 3. Usa como máximo 50 carácteres para tu mensaje de commit

Sé corto y conciso. Si tienes mucho que explicar… seguramente es que tu commit hace demasiadas cosas. ¿Puedes separarlo en diferentes commits? Pues hazlo.

Haz que el mensaje sea claro, directo y que realmente refleje los cambios que lleva.

```ps
$ git commit -m "Add new search feature and change typography to improve performance" # ❌
$ git commit -m "Add new search feature" # ✅
$ git commit -m "Change typography to improve performance" # ✅
```

### 4. Añade todo el contexto que sea necesario en el cuerpo del mensaje de commit

A veces necesitas proveer de más contexto a tu commit. Para ello, en lugar de saturar el sumario del commit, añade información que sea necesaria en el cuerpo del mensaje.

Puedes lograrlo usando **git commit -m "Add summary of commit" -m "This is a message to add more context."** pero en estos casos lo mejor es que uses directamente `git commit` de esta forma:

```ps
$ git commit
```

Y con el editor, podrás añadir un mensaje de commit con saltos de línea fácilmente.

### 5. Usa un prefijo para tus commits para hacerlos más semánticos

Cuando un proyecto crece, es necesario que existan ciertas reglas para que el historial sea legible. Para ello, puedes añadir un prefijo para darle más significado a los commits que realizas. A esto se le llama commits semánticos y se haría de la siguiente manera:

```ps
<tipo-de-commit>[scope]: <descripcion>
```

Por ejemplo:

```ps
feat: add new search feature
^--^  ^--------------------^
│     │
│     └--> # Descripción de los cambios
│
└──------> # Tipo del cambio
```

En monorepositorios multipaquete, puedes añadir también la información del paquete que es afectado por el commit. Se le conoce como `scope` y sería de la siguiente forma

```ps
feat(backend): add filter for cars
fix(web): remove wrong color
```

Estos serían los prefijos:

-   eat: Una nueva característica para el usuario.
-   fix: Arregla un bug que afecta al usuario.
-   perf: Cambios que mejoran el rendimiento del sitio.
-   build: Cambios en el sistema de build, tareas de despliegue o instalación.
-   ci: Cambios en la integración continua.
-   docs: Cambios en la documentación.
-   refactor: Refactorización del código como cambios de nombre de variables o funciones.
-   style: Cambios de formato, tabulaciones, espacios o puntos y coma, etc; no afectan al usuario.
-   test: Añade tests o refactoriza uno existente.

Otra ventaja muy importante de utilizar commits semánticos es que podrás leer el historial de commits para publicar nuevas versiones de un paquete, desplegar nuevas versiones de una aplicación o generar un CHANGELOG con todos los cambios.
