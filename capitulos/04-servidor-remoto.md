# Servidor de repositorios

Para realizar trabajo colaborativo es necesario establecer un repositorio remoto al que accederán dos o más autores.

Idealmente, tal repositorio carece de directorio de trabajo, con lo que se denomina un *bare repository*. Aunque se podrían enviar cambios a un repositorio de un desarrollador (con directorio de trabajo), esto podría resultar confuso, sobre todo para dicho autor. No es la filosofía de *Git*.

## Protocolos

Es necesario decidir qué protocolo(s) soportará el repositorio. *Git* permite cuatro de ellos: local, *HTTP*, *SSH* y *Git*.

### Protocolo local

En este caso el repositorio remoto se halla en el mismo *host* o *filesystem* compartido, como por ejemplo en el caso de un *NFS mount* o *samba*. Para clonar un repositorio remoto y crear uno local:

```
git clone /srv/git/proyecto.git
```

En este caso, se creará una carpeta local ***proyecto***, dentro de la cual se creará una subcarpeta ***.git*** que será aproximadamente igual a la carpeta remota ***/srv/git/proyecto.git***. Seguidamente se hará un *checkout* de la rama principal en local.

Si queremos añadir un remoto de estas características en nuestro proyecto *Git* local:

```
git remote add otro_remoto /srv/git/proyecto.git
```

Este tipo de repositorios es sencillo de usar; a la hora de dar acceso compartido a los distintos usuarios, simplemente hay que gestionar los permisos de archivo desde el sistema operativo.

Cuando se trabaja en la misma red, es posible hacer un *pull* de los cambios de un compañero, simplemente indicando la ruta de su repositorio local (si tenemos permiso para acceder a sus archivos) sin necesidad de pasar por un servidor remoto:

```
git pull /home/pepito/proyecto
```

### Protocolo *HTTP*

Para clonar un proyecto:

```
git clone ht<span>tps://undominio.com/ruta/al/proyecto.git
```

#### Smart *HTTP*

Utiliza métodos de autenticación *HTTPS*, con usuario y contraseña. Más simple que *SSH*, sin tener que usar pares de llaves. Admite acceso anónimo. Si hacemos un *push* y es necesario autenticarse, el servidor simplemente nos solicitará usuario y contraseña. También puede establecerse este mecanismo para lectura (no es frecuente).

Por otro lado, el servidor puede permitir el acceso a un usuario a través de *tokens*. Por ejemplo, se puede crear (en el servidor) un *token* para cada aplicación que quiera acceder al repositorio, o crear uno en cada *host* que utiliza el usuario.

#### Dumb *HTTP*

Raramente usado. Sin autenticación, solo es necesario tener acceso a la *URL* (y en todo caso, privilegios de escritura). Si el servidor no responde al modo anterior, intentará este. En este caso, el repositorio será servido simplemente como archivos normales desde un servidor *web*.

### Protocolo *SSH*

No soporta acceso anónimo. En este caso, es necesario establecer una conexión *SSH* con el servidor. Para clonar:

```
git clone ssh://undominio.com/svr/git/proyecto.git
```

También:

```
git clone undominio.com:svr/git/proyecto.git
```

Para interactuar con un usuario distinto al usuario desde el que estamos autenticados en el equipo local:

```
git clone ssh://usuario@undominio.com/svr/git/proyecto.git
git clone usuario@undominio.com:svr/git/proyecto.git
```

#### Notas sobre autenticación con *SSH* en *GitHub*

Para poder hacer cambios en el repositorio remoto de *github.com*, u otros servidores, podemos usar *ssh*.

En primer lugar habrá que crear el par de claves *RSA* desde el *shell*:

`ssh-keygen -t rsa -b 4096 -C "correo@electronico.com"`

Estos parámetros son los recomendados en el caso de *github.com*, teniendo en cuenta que el correo electrónico debe ser el del usuario de *GitHub*.

Una vez generadas las claves, hay que copiar la clave pública en el apartado de *github.com* habilitado a tal efecto, análogamente a lo que haríamos en un servidor *ssh* (añadiríamos la clave pública en `~/.ssh/authorized_keys`).

En cuanto al lado cliente, se debe añadir la clave privada al *ssh agent*:

`ssh-add <archivo_clave>`

Si el nombre del archivo con la clave privada se generó con el nombre por defecto, puede no ser necesario especificarlo en el comando `ssh-add`.

Existen dos parámetros útiles para `ssh-add`: `-l` lista las claves (identidades) registradas, y `-D` borra las claves.

### Protocolo *Git*

Se suele usar solo para clonado, ya que no posee ningún tipo de autenticación. No es recomendable activar la posibilidad de *push*, ya que cualquiera podría hacerlo.

Es muy rápido, por lo que está indicado para proyectos muy grandes y/o un gran público.

## Crear un servidor de repositorio

En un servidor *Unix*, es habitual colocar los repositorios en un directorio ***/srv/git/proyecto.git***, donde ***proyecto.git*** es el nombre de la carpeta donde se almacenará el proyecto.

Suponiendo que estamos en la carpeta que contiene la carpeta de nuestro proyecto (local), vamos a clonar dicho proyecto a una carpeta con un repositorio *bare* (sin directorio de trabajo). Similar al protocolo local que hemos visto antes:

```
git clone --bare proyecto proyecto.git
```

Esto hará algo parecido a:

```
cp -r proyecto/.git/ proyecto.git
```

Seguidamente habrá que copia esa carpeta ***proyecto.git*** al servidor (normalmente en ***/svr/git***). Una vez hecho esto, habrá añadir permisos de escritura para usuarios autenticados. Esto se hace, **desde la carpeta con el repositorio en el servidor**:

```
git init --bare --shared
```
Con `--bare` evitamos que se cree un *working tree*.

En cuanto a `--shared`, indica cómo proceder en cuanto a permisos sobre los archivos del repositorio a la hora de compartirlo entre distintos usuarios. El formato de este *flag* es `--shared[=valor]`. Este valor puede ser:

- ***umask*** o ***false*** es el valor por defecto cuando no se especifica `--shared`. En este caso, los permisos de los archivos son los definidos en el sistema operativo (en *Linux* sería el *umask*).
- ***group*** o ***true*** es el valor por defecto cuando se especifica `--shared` sin más. En este caso, independientemente de los permisos del sistema, se permite la escritura a los usuarios del grupo (no afecta a los otros bits del *umask*: *owner* y *other*). Relaja siempre el *umask*.
- ***all*** o ***world*** o ***everybody*** es como ***group*** pero además hace el repositorio accesible para lectura a todos los usuarios.
- ***0xxx*** es el número octal, que sustituye al *umask* del sistema. En este caso puede ser más restrictivo que este (o más permisivo).

En todo caso, una vez ejecutado este comando, todo lo que se necesita ahora es dar a los colaboradores acceso *SSH* al servidor.

El problema, si hay muchos colaboradores, es que hay que crear un usuario en el sistema para cada uno de ellos. Esto se puede evitar creando un solo usuario (por ejemplo ***git***) que tenga las claves públicas de todos los colaboradores.

Si queremos dar acceso de lectura a todo el público, deberemos iniciar un *daemon* que sirva el protocolo *Git*.

Alternativamente (o al mismo tiempo), podríamos dar acceso *HTTP* a nuestro repositorio. Esto se hace activando un *script CGI* que lee los *requests* de *fetch* y *push* y los maneja adecuadamente (primero lo intenta mediante el protocolo *smart*, y si no es posible, *falls back* al *dumb*).

## Gitweb

*Git* ofrece la posibilidad de navegar los repositorios mediante un navegador *web*. Existen varias formas, pero si se desea disponer de una *URL* permanente donde examinar los repositorios, se deben seguir las instrucciones detalladas a continuación.

En primer lugar, hay que instalar el paquete *Gitweb*. En distribuciones *Linux* basadas en *Debian*, se haría así:

```
sudo apt-get install gitweb
```

Una vez instalado, podríamos hallar los *scripts* de *Gitweb* en el directorio ***/usr/share/gitweb***, aunque esto dependerá de cada sistema.

Si disponemos de un servidor *HTTP* *Apache*, deberemos habilitar el módulo de *CGI*:

```
sudo a2enmod cgid
```

En este punto, es posible que ya esté toda la configuración hecha automáticamente en *Apache*. En todo caso, deberemos asegurarnos de que la configuración de *Gitweb* está incluida dentro de las configuraciones del servidor. Es posible que (dependiendo del sistema) encontremos el archivo de configuración de *Apache* ***gitweb.conf*** dentro del directorio de configuraciones habilitadas del servidor (***conf-enabled***). En caso contrario, deberemos habilitarla:

```
sudo a2enconf gitweb
```

Podría suceder que este comando fallara debido a que tal configuración no estuviera disponible. En ese caso tendríamos que crear previamente el archivo ***gitweb.conf*** dentro del directorio de configuraciones de *Apache* (***conf-available***). El contenido de tal archivo podría ser algo así:

```
<IfModule mod_alias.c>
    <IfModule mod_mime.c>
        <IfModule mod_cgi.c>
            Define ENABLE_GITWEB
        </IfModule>
        <IfModule mod_cgid.c>
            Define ENABLE_GITWEB
        </IfModule>
    </IfModule>
</IfModule>

<IfDefine ENABLE_GITWEB>
    Alias /gitweb /usr/share/gitweb
    <Directory /usr/share/gitweb>
        Options +FollowSymLinks +ExecCGI
        AddHandler cgi-script .cgi
    </Directory>
</IfDefine>
```

Lo que hace esta configuración de ejemplo es, previa comprobación de la existencia de módulos, asociar la *URI* ***/gitweb*** al *script Gitweb*, y establecer opciones en el directorio objetivo.

Por otro lado, es necesario configurar la aplicación *Gitweb* en sí, especialmente para indicar la ruta en la que se hallan los repositorios. Para ello deberemos editar el archivo de configuración de *Gitweb*, que en sistemas *Linux* podríamos encontrar en ***/etc/gitweb.conf***.

En este archivo deberemos configurar la variable ***\$projectroot***, que estableceremos a la ruta donde se encuentran nuestros repositorios.

En el directorio raíz de un proyecto (no en el *working dir*, sino en el raíz del *bare repository*, en el servidor) podemos crear archivos que proporcionan información sobre el mismo. Especialmente útiles son los archivos ***description*** (o ***gitweb.description***) y ***category*** (o ***gitweb.category***), utilizados para visualizar una descripción corta del proyecto (una línea), y para asociarlo a una categoría. A la hora de visualizar los repositorios, estos son agrupados por categorías (si no tienen tal archivo de categoría, pertenecen a la categoría ***default***).
