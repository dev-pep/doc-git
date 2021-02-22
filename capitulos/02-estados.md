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

Los sucesivos cambios que se realizan en archivos *tracked* son cambios no *staged* (cambios *unstaged*), con lo cual el archivo está en estado *modified*, hasta que añadimos el archivo al *staging area* mediante `git add <archivo(s)>`.

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

## Áreas

Dependiendo del estado de un archivo del repositorio, residirá en una u otra área de las siguientes:

- El directorio de trabajo
- La *staging area*
- El repositorio en sí

### Directorio de trabajo

El directorio de trabajo (*working tree*) es una instantánea, o un *checkout* del repositorio. Son los archivos del proyecto, en una versión concreta.

Si nos desplazamos por las distintas versiones (*commits*), hacia adelante o atrás, por cualquiera de las ramas del repositorio, veremos como los archivos del directorio de trabajo cambian a medida que son *pulled* del repositorio local: su contenido cambiará, unos archivos o carpetas aparecerán, otros desaparecerán, etc.

Los cambios realizados en el directorio de trabajo pueden ser *staged*.

### Staging area

Es un archivo en el repositorio (dentro del directorio `.git`) con información sobre todo lo que entrará en el próximo *commit*.

La *staging area* puede contener algunos cambios realizados en un archivo, mientras otros cambios en el mismo archivo pueden estar *unstaged* (es decir, en estado *modified*) si hemos realizado estos cambios después de haber pasado el archivo a la *staging area* con `git add <archivo>`. Solo los cambios *staged* se incluirán en el *commit*.

Se puede hacer *commit* de todos los cambios (exceptuando los archivos *untracked*) con este comando:

`git commit -a -m "Mensaje del commit"`
