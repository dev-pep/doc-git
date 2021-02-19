# Estados del repositorio

Para ver el estado del repositorio:

`git status`

## Untracked y tracked

Cuando añadimos (creamos) un nuevo archivo dentro de la carpeta del repositorio, *Git* no lo rastrea. Para que pase a formar parte del sistema de control de versiones, teclearemos:

`git add <nombrearchivo>`

Esto añade el archivo llamado \'\<nombrearchivo\>\' al repositorio (pasa de *untracked* a *tracked*).

## Modified y staged

Además, el comando anterior coloca el archivo en la *staging area*, es decir, listo para formar parte del siguiente *commit* que realicemos.

Si deseamos incluir todo el contenido de la carpeta del proyecto al repositorio y a la *staging area*, se puede teclear:

`git add .`

Esto añade todos los archivos y carpetas, excepto las carpetas vacías, que son invisibles para *Git*.

Los sucesivos cambios que se realizan en archivos *tracked* son cambios no *staged* (cambios *unstaged*), con lo cual el archivo está en estado *modified*, hasta que añadimos el archivo al *staging area* mediante `add`.

## Committed

El comando:

`git commit`

pasa todos los cambios *staged* a un nuevo *commit* local, en la base de datos de la subcarpeta `.git`. Crea una nueva instantánea (versión) del repositorio en el equipo local.

Si tecleamos el comando del modo especificado, *Git* abrirá un editor de texto para que especifiquemos un mensaje representativo de ese *commit*.

Podemos especificar el mensaje en el mismo comando para evitar que se abra el editor de texto:

`git commit -m "Mensaje representativo de este commit"`

Si deseamos añadir también una descripción del mismo incluiremos dos mensajes:

`git commit -m "Mensaje de este commit" -m "Descripción más detallada"`

## Push

Podemos ir realizando todos los *commits* que deseemos en modo local. Para subirlos al servidor remoto, debemos teclear:

`git push`

Todos los *commits* existentes pasarán a añadirse al repositorio remoto.
