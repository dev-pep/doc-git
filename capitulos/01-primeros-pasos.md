# Primeros pasos en Git

Para poder gestionar repositorios en un servidor remoto, un cliente *Git* debe ser instalado en el host local.

## Clonar un repositorio remoto

Una vez se ha instalado *Git* localmente, podemos clonar un repositorio de un servidor. Supongamos que el servidor es <https://www.github.com>:

`git clone https://github.com/dev-pep/doc-git.git`

Donde `dev-pep` es el nombre del propietario del repositorio remoto, y `doc-git` es el nombre del repositorio.

Este comando creará una carpeta \'doc-git\', con el repositorio, dentro de la carpeta actual. Si queremos que el repositorio descargado se clone en una carpeta de distinto nombre, como \'proyDir\' teclearemos:

`git clone https://github.com/dev-pep/doc-git.git proyDir`

Esta modalidad utilizará el protocolo *https* para la autenticación, es decir, que cuando queramos realizar cambios en el repositorio remoto, nos pedirá usuario y contraseña, que deberán ser un usuario y contraseña con privilegios de *commit* en ese repositorio. Este método de autenticación está destinado a desaparecer de *github.com*.

Si por el contrario deseamos usar *ssh* para hacer los *commits*, tendremos que clonar mediante:

`git clone git@github.com:dev-pep/doc-git.git`

Más adelante veremos cómo utilizar *ssh* en *github.com*.

## Crear un nuevo repositorio

Es sencillo crear un nuevo repositorio vacío en *github.com*. Solo hay que seguir unos sencillos pasos en la web.

Para crear un repositorio localmente, crearemos en primer lugar la carpeta de dicho repositorio. Luego, desde el interior de esa carpeta se tecleará:

`git init`

Esto creará el directorio oculto `.git`, con lo que dispondremos ya de un repositorio *Git*, con una rama por defecto llamada *master*. Si el directorio contenía archivos, habrá que añadirlos al repositorio como se verá más adelante. Tanto si está vacío como si ya hemos creado archivos y realizado algún *commit*, se debe indicar a *Git* la ubicación del repositorio remoto donde realizar los *push*. Para ello, teclearemos:

`git remote add origin git@github.com:dev-pep/doc-git.git`

En este caso utilizamos el modo de autenticación con *ssh*.
