# Configuración de *Git*

Existen varios parámetros configurables en *Git*. La forma de establecer su valor es:

`git config <variable> <valor>`

En primer lugar, para poder hacer los *commits*, es necesario indicar la identidad del usuario que realiza dichos *commits*. Las variables que podemos usar para esto son `user.email` o `user.name`:

```
git config user.name "El Pepe"
git config user.email correo@electronico.com
```

Estos valores no tienen efectos de autenticación, sino únicamente aparecerán asociados a los *commits* que realice el usuario.

Tal como están tecleados estos comandos (o incluyendo `--local`), estas configuraciones tendrán efecto únicamente en el repositorio local concreto. Estas configuraciones se suelen guardar en el archivo ***config*** del directorio ***.git*** del repositorio local.

Si deseamos que las configuraciones especificadas afecten a todos los repositorios del usuario, deberemos teclear:

```
git config --global user.name "El Pepe"
git config --global user.email correo@electronico.com
```

Estas configuraciones se suelen almacenar en ***~/.gitconfig*** o ***~/.config/git/config***.

Si en lugar de `--global` indicamos `--system`, la configuración afectará a todos los repositorios de todos los usuarios del sistema. La configuración suele estar en ***/etc/gitconfig***.

La configuración prioritaria es la local, que tiene preferencia sobre la configuración global, que a su vez tiene preferencia sobre la configuración de *Git* del sistema.

Otra variable que puede resultar útil es `core.editor`, que indica a *Git* qué editor debe abrirse, por ejemplo, al realizar un *commit* sin indicar mensaje.

```
git config --global core.editor vim
```

Para eliminar una configuración concreta (del nivel que deseemos):

```
git config --global --unset core.editor
```

Es posible especificar un archivo que contendrá un texto por defecto, que aparecerá cada vez que se abra el editor al hacer un *commit*.

```
git config commit.template ~/texto.txt
```

Si se está desarrollando en el penoso sistema *Windows*, podemos configurar:

```
git config core.autocrlf true
```

En este caso, *Git* incluye en el repositorio las versiones con saltos de línea ***LF***, como en los sistemas buenos; al hacer el *checkout* (en *Windows*) lo hará con las versiones con ***CRLF***.

Con esta opción:

```
git config core.autocrlf input
```

*Git* cambia los posibles ***CRLF*** por ***LF*** al hacer los *commits*, pero los mantiene como ***LF*** también al hacer el *checkout*.

Para desactivar este mecanismo:

```
git config core.autocrlf false
```

## Credenciales

Con la conexión *SSH* no es necesario teclear una contraseña para acceder al repositorio. Sin embargo, con *HTTP* sí es necesario cada vez. El sistema de credenciales de *Git* tiene varios niveles:

- Por defecto, no hay cache de credenciales. Se pregunta usuario y contraseña a cada conexión que precise autenticación.
- El modo *cache* mantiene las credenciales en memoria (no en disco) un pequeño lapso de tiempo (se purga a los 15 minutos).
- El modo *store* guarda las credenciales en un archivo de texto plano. No expira. Poco seguro.

Se puede establecer el sistema que deseemos:

```
git config credential.helper cache
```

Si establecemos *store*, podemos especificar el nombre del archivo de texto:

```
git config credential.helper 'store --file ~/archivo.txt'
```

### Token

Algunos servidores (como *GitHub*) permiten establecer un *token* de autenticación. Desde la *web* se crea dicho *token*, que se debe configurar a la hora de crearlo (hay que definir para qué capacidades lo creamos). Podemos tener varios *tokens*, para varios usos, o asociar cada token a una aplicación (editor de texto, IDE, etc.).

A partir de ese momento, la autenticación (por *HTTP*) se hace usando el nombre de usuario habitual, pero en lugar de la contraseña, uno de los *tokens*. De este modo definimos qué cosas se podrán hacer con esa autenticación y qué cosas no.
