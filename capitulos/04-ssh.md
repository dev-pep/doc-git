# Autenticación con ssh

Para poder hacer cambios en el repositorio remoto de *github.com*, u otros servidores, podemos usar *ssh*.

En primer lugar habrá que crear el par de claves RSA:

`ssh-keygen -t rsa -b 4096 -C "correo@electronico.com"`

Estos parámetros son los recomendados en el caso de *github.com*, teniendo en cuenta que el correo electrónico debe ser el del usuario de *github*.

Una vez generadas las claves, hay que copiar la clave pública en el apartado de *github.com* habilitado a tal efecto, análogamente a lo que haríamos en un servidor *ssh* (añadir la clave pública en `.ssh/authorized_keys`).

En cuanto al lado cliente, se debe añadir la clave privada al *ssh agent*:

`ssh-add <archivo_clave>`

Si el nombre del archivo con la clave privada se generó con el nombre por defecto, puede no ser necesario especificarlo en el comando `ssh-add`.

Existen dos parámetros útiles para `ssh-add`: `-l` lista las claves (identidades) registradas, y `-D` borra las claves.
