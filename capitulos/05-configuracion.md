# Configuración de Git

Existen varios parámetros configurables en *Git*. La forma de establecer su valor es:

`git config <variable> <valor>`

En primer lugar, para poder hacer los *commits*, es necesario indicar la identidad del usuario que realiza dichos *commits*. Las variables que podemos usar para esto son `user.email` o `user.name`:

```
git config user.name "El Pepe"
git config user.email correo@electronico.com
```

Estos valores no tienen efectos de autenticación, sino únicamente aparecerán asociados a los *commits* que realice el usuario.

Tal como están tecleados estos comandos, estas configuraciones tendrán efecto únicamente en el repositorio local concreto. Si deseamos que afecten a todos los repositorios del usuario local, deberemos teclear:

```
git config --global user.name "El Pepe"
git config --global user.email correo@electronico.com
```

Si en lugar de `--global` indicamos `--system`, la configuración afectará a todos los repositorios de todos los usuarios del sistema.

La configuración prioritaria es la local (almacenada normalmente en `.git/config`), que tiene preferencia sobre la configuración global (normalmente en `~/.gitconfig`), que a su vez tiene preferencia sobre la configuración de *Git* del sistema (normalmente en `/etc/gitconfig`).

Otra variable que puede resultar útil es `core.editor`, que indica a *Git* qué editor debe abrirse, por ejemplo, al realizar un *commit* sin indicar mensaje.

`git config --global core.editor vim`

## Obtener la configuración

Para ver qué configuración maneja nuestro repositorio, teclearemos:

`git config --list`

Este comando tendrá en cuenta las distintas configuraciones (local, global y de sistema). Si una variable aparece más de una vez es porque está definida en más de una de estas configuraciones. En este caso la que se utiliza es la última. En cambio:

`git config --global --list`

no tendrá en cuenta la configuración local, mientras que:

`git config --system --list`

solo tendrá en cuenta la configuración de sistema.

Para ver el valor de una variable concreta:

`git config user.name`

Del mismo modo, utilizando `--global` no se leerá la configuración local, mientras que con `--system` solo se leerá la de sistema.
