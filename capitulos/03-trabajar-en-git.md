# Trabajar en Git

## El repositorio

El repositorio, con información de todas las instantáneas, reside en el directorio `.git`. Hacer un *commit* añade una nueva instantánea al repositorio con los cambios de la *staging area*. Se puede hacer un *checkout* de una de las instantáneas en el directorio de trabajo.

Para ver los *commits* del repositorio, así como la posición actual, teclearemos:

`git log`

## Sincronización con el repositorio remoto

En relación al servidor remoto centralizado, se pueden subir los *commits* realizados localmente al servidor mediante un `git push` para sincronizar los contenidos. Por otro lado, en el servidor pueden existir *commits* hechos por otros usuarios que convendrá descargar. Esto se hace mediante un `git pull`.

## Estado abreviado

A parte de `git status`, podemos obtener el estado actual del repositorio de forma abreviada, usando `-s` o `--short`:

`git status --short`

En el resultado, aparece cada archivo del directorio de trabajo junto a un código de dos caracteres. El primer carácter indica el estado en la *staging area*, mientras el segundo indica el estado en el árbol de trabajo. El carácter ***A*** indica archivo añadido, ***M*** modificado, o ***?*** *untracked*. Un espacio indica sin cambio.

Por ejemplo:

```
MM archivo1.md
 M archivo2.md
M  archivo3.md
A  archivo4.md
?? archivo5.md
```

En este ejemplo, el archivo \'archivo1.md\' tiene cambios en el área de *staging* y también tiene cambios *unstaged*. En cambio, \'archivo2.md\' solo tiene cambios *unstaged*, mientras \'archivo3.md\' solo tiene cambios en el área de *staging*. \'archivo4.md\' ha sido añadido al repositorio, y será incluido en el próximo *commit*, mientras que \'archivo5.md\' está *untracked*.

## Ignorar archivos

Se puede especificar qué archivos deseamos que *Git* ignore, incluyendo en cada carpeta procedente un archivo `.gitignore` que especifique los archivos a ignorar dentro de la carpeta.

`.gitignore` permite especificar patrones de nombres de archivo. Cada patrón se aplica recursivamente al directorio actual y a los subdirectorios. Veamos algunas peculiaridades del formato de este archivo:

- Las líneas vacias y las que empiezan con ***#*** son ignoradas.
- Se permiten los *wildcards* `*` y `?`.
- El prefijo `!` niega el patrón.
- Si el patrón termina en `/` indica subdirectorio (ignorar todo su contenido).
- Un conjunto de caracteres del tipo `[abcABC]` se hará coincidir con un carácter. También se admiten rangos, del tipo `[a-z]`.
- Si el patrón es precedido por `/`, indica que solo se refiere a los archivos del directorio actual (evita recursión).

## Diferencias

Podemos ver los cambios pendientes que todavía no están *staged*:

`git diff`

Para ver los cambios en el área de *staging*:

`git diff --staged`

o su equivalente:

`git diff --cached`

## Eliminación de archivos

Para eliminar un archivo (o archivos) del repositorio y borrarlos del árbol de trabajo:

`git rm <archivo(s)>`

Si tecleamos:

`git rm --cached <archivo(s)>`

se elimina ese archivo del repositorio, pero no se borra de disco (pasa a *untracked*).

## Mover o renombrar archivos

Para mover un archivo (o renombrarlo) de tal modo que *Git* no le pierda la pista, en lugar de hacer esta operación desde el sistema operativo, es mejor usar el comando:

`git mv <org> <dest>`

# Retocar un commit erróneo

Si hemos hecho un *commit* y descubrimos que hay algún error en él, podemos usar:

`git commit --amend`

Supongamos que acabamos de hacer un *commit* y nos damos cuenta de que simplemente el mensaje asociado era erróneo. Simplemente, ejecutamos el comando especificado, con el mensaje correcto.

Supongamos que el error es que faltaban cambios en el área de *staging*. Entonces, tras el *commit* erróneo hacemos todos los cambios necesarios al *staging* y ejecutamos nuevamente el comando.

Cuando estamos colaborando con varias personas, es mejor no retocar *commits* que hayan sido ya *pushed* al repositorio remoto, para no liar a los colaboradores.

# Revertir Estados

Para quitar un archivo (o archivos) del área de *staging* y pasarlo simplemente a *modified* (no altera su contenido):

`gif restore --staged <archivo(s)>`

Para revertir los cambios que se han hecho a un archivo *modified* (cambia su contenido al estado tras el último *commit*):

`gif restore <archivo(s)>`
