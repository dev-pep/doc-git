# *Branching*

Al hacer un *commit*, *Git* crea un objeto *commit* con un apuntador al último *snapshot*, que contiene los cambios *staged*. Este objeto contiene también el nombre del autor, su correo (campos obligatorios), el mensaje descriptivo del *commit*, y apuntadores al *commit* precedente (padre): cero apuntadores si es el *commit* inicial del repositorio, un apuntador si es un *commit* normal, y dos o más apuntadores si es un *commit* resultante de una fusión de dos o más ramas (en ese caso tendrá dos o más padres).

En el repositorio podemos encontrar, a parte de objetos *commit*, los archivos del repositorio en sí, almacenados mediante objetos **árbol**, que definen un directorio del proyecto, y objetos archivo, llamados **blobs**. El objeto árbol apunta a los *blobs* y a otros objetos árbol que contiene. Todos estos objetos tienen su propio *checksum*. Así, un *commit* apunta al objeto árbol raíz, a partir del cual se puede obtener la instantánea de todo el proyecto.

En cada nuevo *commit*, como mínimo se creará un nuevo **blob** (si solo hemos cambiado o añadido un archivo), así objetos árbol nuevos correspondientes a la carpeta donde se hallaba el archivo correspondiente al nuevo **blob**, y todas las carpetas ascendientes. Lógicamente también se creará el objeto *commit* correspondiente.

Una **rama** no es más que un objeto rama, que contiene un apuntador a un objeto *commit*, que es el *commit* más reciente correspondiente a esa rama.

Para crear una rama nueva hacemos:

```
git branch <nombreRama>
```

Esto lo que hace es crear un objeto rama con el nombre especificado. Esta rama es un apuntador al *commit* en el que nos hallamos en el momento de crearla. Por otro lado, aunque hayamos creado la rama, no hemos cambiado a esta, sino que seguimos en la anterior. Para saber en qué rama nos hallamos, *Git* usa un apuntador llamado ***HEAD*** que apunta al *commit* último de la rama donde nos hallamos (rama actual).

Si queremos cambiar a otra rama podemos usar cualquiera de estos dos comandos:

```
git switch <nombreRama>
git checkout <nombreRama>
```

Esto mueve ***HEAD*** al *commit* terminal de la rama especificada.

Si queremos crear una rama y **al mismo tiempo** establecerla como rama actual, se puede hacer en un solo comando, usando cualquiera de estas formas:

```
git switch -c <nombreNuevaRama>
git switch --create <nombreNuevaRama>
git checkout -b <nombreNuevaRama>
```

Para regresar a la rama en la que estábamos anteriormente:

```
git switch -
```

> Al cambiar de rama, el contenido del directorio de trabajo cambia al estado (instantánea) definido por el último *commit* de la rama.

Cuando hacemos un nuevo *commit*, se crea un nuevo objeto *commit* cuyo padre es el último *commit* de la rama actual, es decir el *commit* al que apunta el objeto rama actual. Entonces, el objeto rama pasa a apuntar al *commit* recién creado (y ***HEAD*** sigue apuntando a ese objeto rama actual).

> Cuando teclemos `git log` solo aparece el historial de *commits* de la rama actual. Si queremos ver el de otra rama, hay que teclear `git log <nombreRama>`. Si queremos verlas todas, `git log --all`.

## *Branching* y *merging* básico

Ya hemos visto cómo crear ramas y cambiar de una a otra. De momento asumiremos que al cambiar no dejamos cambios *modified* o *staged*, es decir, antes de cambiar hacemos *commit* de todos los cambios.

Supongamos que estamos trabajando en una rama ***hotfix***, que se dividió desde la rama ***master*** y que tras las pruebas, vemos que está todo finalizado y correcto. Para fusionar la rama con la ***master***:

```
git switch master
git merge hotfix
```

Si la fusión es tan simple como avanzar la rama ***master*** el punto donde se halla la rama ***hotfix***, es decir, si no se encuentran caminos divergentes en ese recorrido, esto es lo que hace *Git* (se denomina hacer un *fast forward*, o *two-way merge*).

Sea como sea, una vez la rama ***master*** apunta al mismo *commit* que la rama ***hotfix***, ya se puede eliminar esta última:

```
git branch -d hotfix
```

Supongamos que partiendo de la rama ***master*** creamos otra rama ***issue53***. Vamos haciendo cambios en esta última. Si al mismo tiempo se vamos añadiendo cambios en la rama ***master***, dichos cambios no están reflejados en ***issue53***. Para que lo estén deberíamos hacer *pull* de la rama ***master*** en nuestra rama ***issue53*** (`git merge master`), o podemos esperar a terminar nuestro trabajo en ***issue53*** sin interferir con el desarrollo de ***master***, y hacerlo al revés, fusionando luego nuestra rama con la principal.

Así, supongamos que hemos terminado nuestro trabajo en la rama ***issue53***, y que al mismo tiempo también hemos realizado algunos cambios en ***master***. Vamos a fusionar la rama de trabajo en la principal:

```
git switch master
git merge issue53
```

En este caso, dado que ambas ramas han ido por caminos divergentes (el camino de la rama de trabajo no es descendiente directa del *commit* de la rama principal), no es aplicable el *fast forward*.

Lo que hace *Git* en este caso es lo que se denomina un *three-way merge*, ya que hay 3 *commits* involucrados:
- El último *commit* de ***master***.
- El último *commit* de ***issue53***.
- El *common ancestor* a ambos, es decir, el *commit* anterior a la divergencia de caminos.

Este *merge* compara el *snapshot* del ascendiente común con el último *snapshot* de la primera rama, para ver qué cambios se han realizado en esta. Hace lo propio con la segunda rama, para ver los cambios realizados en la otra rama. Si ambos conjuntos de cambios se pueden aplicar sin ningún conflicto, **se crea un nuevo *commit*** que incorpora los cambios de ambas ramas. Este nuevo *commit* tiene dos padres: el último *commit* de una rama y el de la otra. Por último, la rama a la que se fusiona avanza hasta el nuevo *commit* creado. La rama que se fusionaba con la principal queda apuntando al mismo lugar, aunque lo más normal en este punto es eliminar dicha rama.

Si a la hora de la fusión se detectan conflictos (hay cambios en el mismo archivo y línea en ambas ramas), se deberán resolver dichos conflictos manualmente antes de seguir adelante (*Git* crea un conflicto que se debe resolver a mano).

Hay que decir que al crearse un nuevo *commit* de fusión al ejecutar el *merge*, si no hemos especificado el mensaje descriptivo (con `-m`), nos aparecerá el editor para que indiquemos tal mensaje.

### Conflictos básicos de fusionado de ramas

Cuando se detecta un conflicto al fusionar ramas, no se crea el *commit* de fusionado. *Git* avisa que el proceso ha sido pausado y precisa solución manual.

En este caso, el problema reside en que uno o más archivos han sido editados en el mismo punto en ambas ramas. Para saber qué archivos están afectados, podemos hacer un `git status`. Esto nos informará de qué archivo o archivos están en estado *unmerged*. Lo interesante es que *Git* añade la información del conflicto en los archivos afectados, de tal modo que solo hay que editarlo para resolver los conflictos. Podríamos encontrarnos algo así en el  ***index.html***:

```
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

En este caso, ***HEAD*** indica la rama actual (a la que fusionar), mientras que ***iss53*** es la rama que deseamos fusionar en la actual.

Solo hay que editar el archivo ***index.html*** y dejarlo con la versión que deseemos (que puede ser una de las dos alternativas que nos presenta, una fusión de ambas, o lo que queramos). Tras guardarlo, se debe añadir el archivo a la zona de *staging* (con `add`). Cuando todos los archivos *unmerged* están editados y *staged*, ya podemos proceder a hacer un *commit*, que en este caso será el de fusión de las ramas.

Mientras estamos en el proceso de resolución de conflictos no podemos cambiar de rama ni hacer *checkout* de otro *commit*. Si, en cualquier momento, deseamos abortar el proceso de resolución y regresar al momento justo antes de ejecutar la instrucción `merge`, podemos hacerlo:

```
git merge --abort
```

## Gestión de ramas

Si tecleamos `git branch` sin argumentos, recibiremos información de todas las ramas presentes en el repositorio, y cuál de ellas es la actual.

Si además deseamos ver el último *commit* de cada rama, teclearemos `git branch -v`.

Tecleando `git branch --merged` vemos la lista, pero solo aparecerán las ramas que están fusionadas con la rama actual. `git branch --no-merged` hace lo contrario. Estas dos opciones pueden usarse, en lugar de con referencia a la rama actual, con referencia a cualquier otra rama:

```
git branch --no-merged master
```

Esto nos informará de todas las ramas que no se han fusionado con la rama ***master***.

No es posible borrar (con `-d`) una rama que no esté fusionada en ninguna otra rama. Para forzar tal borrado hay que hacerlo con la opción `-D`.

### Asociación de ramas locales y remotas

En primer lugar, indicar que el comando `git push`, para enviar *commits* al repositorio remoto, se puede usar así:

```
git push <remoto> <rama>
```

Si se omiten los argumentos, se usará el remoto por defecto (normalmente ***origin***) y la rama actual.

Todos los *commits* de un repositorio deberían estar en una rama (puede no ser así). Supongamos que tenemos un repositorio vacío en un servidor, sin ramas ni contenido. Lo clonamos en local y hacemos *commits*. Al realizar el primer `git push`, se creará automáticamente una rama ***master***, tanto en el repositorio local como en el remoto (aunque se podrían crear manualmente estas ramas con anterioridad). A partir de ese punto, toda gestión de ramas se debe hacer a mano.

Supongamos que creamos ahora una rama nueva en este repositorio:

```
git branch rama-nueva
```

La salida del comando `git branch --all` será:

```
* master
  rama-nueva
  remotes/origin/master
```

La opción `--all` muestra también las ramas correspondientes a los remotos. Tenemos, pues, nuestras dos ramas locales y una rama (llamada **rama *upstream***) que representa a la rama ***master*** en remoto ***origin***.

Una rama *upstream* es una rama local que representa el estado del remoto. Esta rama *upstream* debe estar asociada a una rama local del mismo nombre, que hace el seguimiento de su contenido (se sincroniza con ella).

Ahora, hacer `git push` se enviarán los *commits* pendientes de la rama actual (de momento, ***master***).

Hacemos ahora `git switch rama-nueva` y realizamos uno o más *commits* en esa rama. Si entonces introducimos `git push`, *Git* nos informará de que la rama ***rama-nueva*** no tiene una rama *upstream* asociada. Para salir del paso, podríamos indicarle explícitamente:

```
git push origin rama-nueva
```

Esto hace varias cosas: en primer lugar, crear la rama ***rama-nueva*** en el servidor, así como una rama ***remotes/origin/rama-nueva*** localmente. Lo podemos comprobar con `git branch`:

```
master
* rama-nueva
remotes/origin/master
remotes/origin/rama-nueva
```

Podría parecer que la rama ***remotes/origin/rama-nueva*** es la rama *upstream* asociada a la rama ***rama-nueva***. Sin embargo, si volvemos a hacer *commits* en esa rama, al volver a ejecutar `git push`, *Git* nos volverá a decir que ***rama-nueva*** no tiene rama *upstream* asociada, con lo que cada vez que deseemos hacer un `push` de esa rama deberemos especificar remoto y nombre de rama.

Para que ***rama-nueva*** esté ligada a ***remotes/origin/rama-nueva*** de tal modo que sea su rama *upstream* asociada, hay que teclear el comando:

```
git push --set-upstream <remoto> <rama>
```

Es decir:

```
git push --set-upstream origin rama-nueva
```

Recordemos que ambas ramas deben tener el mismo nombre (la local y la *upstream*, que tiene el mismo nombre a su vez que en el remoto).

A partir de ese momento, ***rama-nueva*** hace un seguimiento (se sincroniza) con el contenido de ***remotes/origin/rama-nueva***, que es el reflejo del contenido de ***rama-nueva*** **en el servidor**. Cuando solicitamos el estado de una rama del servidor (mediante `git fetch`), estos *commits* van a la rama *upstream* local (***remotes/origin/rama-nueva***), que luego se deben fusionar (con `git merge`) en la rama local (***rama-nueva***), como una fusión normal.

Supongamos que volvemos a la rama principal y hacemos una nueva rama:

```
git switch master
git branch otra-rama
```

Podemos crear de una vez la rama en el remoto, lo cual creará la rama local correspondiente a ese remoto, y asociar dicha rama del remoto a la rama local, como su rama *upstream*:

```
git push --set-upstream origin otra-rama
```

Ahora, todos los cambios que realicemos en ***otra-rama*** se podrán enviar al servidor simplemente con `git push`, y podremos obtener todos los *commits* de esa rama procedentes del servidor.

### Renombrar una rama

> No se debe renombrar una rama que puedan estar editando otros colaboradores.

Para renombrar una rama local:

```
git branch --move <nombreAntiguo> <nombreNuevo>
```

Si solo le damos un argumento, este será el nombre nuevo de la **rama actual**. En lugar de `--move` se puede usar `-m`.

Supongamos que seguimos en el ejemplo anterior, y renombramos la última rama creada:

```
git branch --move otra-rama ramita
```

Si queremos crear la rama también en el remoto, debemos tener en cuenta que para el remoto ***ramita*** es una **nueva rama**, con lo que tendremos que crear y asociar la rama *upstream* correspondiente:

```
git push --set-upstream origin ramita
```

Esto crea la rama en el remoto, y la rama *upstream* local correspondiente, debidamente asociada a la rama local ***ramita***. Sin embargo, si hacemos `git branch --all` obtenemos:

```
* master
  rama-nueva
  ramita
  remotes/origin/master
  remotes/origin/otra-rama
  remotes/origin/rama-nueva
  remotes/origin/ramita
```

Como vemos, en el servidor no se ha eliminado la rama con el nombre antiguo (***otra-rama***), con lo que tendremos que hacerlo manualmente:

```
git push -d origin otra-rama
```

La opción `-d` también se puede escribir `--delete`.

Si se desean eliminar y/o crear varias ramas de un plumazo en el remoto, se pude hacer añadiendo dos puntos (***:***) antes de las ramas a eliminar, y sin añadir nada en las ramas a crear:

```
git push origin :rama1 :rama2 rama3 :rama4
```

Esto añade en el servidor la ***rama3***, y elimina ***rama1***, ***rama2*** y ***rama4***. Lo que no hace es definir ramas como *upstream*, que deberá hacerse posteriormente.

Otra forma de realizar el cambio de nombre, por ejemplo, sería:

```
git branch -m rama-nueva produccion
git push origin :rama-nueva produccion
git push --set-upstream origin produccion
```

> La rama principal no debería renombrarse. Es posible pero puede ser complejo y pocas veces conveniente.

## Ramas remotas

Aquí entran en juego las ramas *upstream* o ramas remotas de las que hemos hablado.

Aunque sean locales, son el reflejo del estado del servidor remoto en cuestión.

Supongamos que clonamos un repositorio. Tenemos nuestra rama ***master*** y nuestra rama ***origin/master*** apuntando ambas al último *commit*. Si hacemos *commits* en nuestra rama ***master***, esto no afecta a ***origin/master***, que permanece en el sitio del clonado.

Al mismo tiempo alguien hace algunos *pushes* en el remoto. Para sincronizar nuestro trabajo con el remoto, debemos hacer:

```
git fetch <remoto>
```

El remoto, en este caso, es ***origin***, con lo que podríamos obviarlo.

Esto lo que hará será descargar todos los nuevos *commits* en la rama ***origin/master***. Si los queremos fusionar, lo haremos mediante:

```
git merge origin/master
```

Si omitimos el nombre de la rama, se considera que el remoto es ***origin*** y la rama es la actual.

Por otro lado, hemos visto ya el comando `git push <remoto> <rama>`. Para poder realizar un envío de nuestros *commits* a una rama concreta del remoto, nuestra rama debe incluir todos los *commits* que han realizado los demás, por lo que antes del *push* hay que hacer un *fetch* y fusionarlo con nuestra rama local. Una vez hecho esto, ya podemos hacer el *push*.

Por defecto, nuestra rama local tendrá el mismo nombre que en remoto, pero si quisiéramos que en local tuviese un nombre distinto, al hacer el *push* deberíamos hacer:

```
git push origin nombre-local:nombre-remoto
```

Una rama local asociada a una rama *upstream* se denomina una *tracking branch*. Si nos hallamos en una *tracking branch*, podemos teclear el comando:

```
git pull
```

En este caso, *Git* ya sabe en qué remoto y rama buscar, y lo que hace es un *fetch* seguido del correspondiente *merge* automáticamente.

Al clonar un repositorio, no se crean localmente todas las ramas existentes en el remoto, sino únicamente la principal (las ramas remotas sí se crean). Si deseamos crear otras ramas que hagan seguimiento de ramas remotas específicas, hay que teclear:

```
git checkout -b <ramaLoc> <remoto>/<ramaRem>
```

Si el nombre de nuestra rama local va a ser el mismo que en el remoto (es lo habitual):

```
git checkout --track <remoto>/<rama>
```

Hay una forma todavía más corta, siempre y cuando el nombre de la rama exista únicamente en uno de nuestros remotos:

```
git checkout <rama>
```

## *Rebasing*

El *rebasing* es otra forma de fusionar ramas. Supongamos que tenemos dos caminos divergentes: las ramas ***master*** y ***iss53***. Si hacemos:

```
git switch iss53
git rebase master
```

Esto hace lo siguiente:
- toma como base el ancestro común de las dos ramas.
- obtiene el parche (*patch*) de ***iss53***, es decir, los cambios (*diff*) que aplicados al ancestro común producen la última versión de ***iss53***.
- cambia la rama ***iss53*** para que pase a apuntar al mismo *commit* que ***master*** (en este punto, todos los *commits* de ***iss53*** quedan huérfanos de rama).
- siguiendo en la rama ***iss53*** aplica el parche.

De esta forma, digamos que los cambios de la rama ***iss53*** se han aplicado a partir del último *commit* de ***master***, con lo que ***iss53*** a adelantado a ***master*** (va un *commit* por delante). Ahora solo hay que hacer un *fast-forward* en ***master***:

```
git switch master
git merge iss53
```

Como norma general, un *rebase* queda más limpio que un *merge* de ramas, ya que eliminamos todos los *commits* de una rama, y los sustitiomos por uno solo (el parche aplicado). Sin embargo, esto solo puede hacerse si todo el trabajo en esa rama que vamos a dejar fuera se ha hecho en local. Si hemos hecho *push* de esa rama en algún momento ya no se debe hacer, porque quedarían *commits* sin rama en el servidor.

## Organización del proyecto

Un proyecto se puede organizar de una infinidad de formas distintas con *Git*. Puede haber más de un servidor remoto; se pueden organizar los servidores por secciones del proyecto, o trabajar todos contra el mismo servidor.

En el caso de varios remotos, se podría trabajar en una rama distinta en cada servidor. En este caso, debería haber algún miembro del proyecto, o algún grupo del mismo que se encargara de acceder a todos los remotos (es decir, a todas las ramas) para, cuando fuese necesario fusionar su contenido con la rama principal. El resto de departamentos no tendría por qué tener acceso a esas ramas, con lo que solo tendrían acceso a los cambios de estas cuando estuviesen fusionados con la rama principal.

Las posibilidades de organización son ilimitadas.
