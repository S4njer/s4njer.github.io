---
layout: post
title: 'Lectura e interpretación de permisos'
date: 2023-02-14
categories: [Lectura-Permisos, Linux]
---
### Lectura e interpretación de permisos
Permisos y Derechos en Linux: https://blog.desdelinux.net/permisos-y-derechos-en-linux/?msclkid=22f8cb88ba8111ecb5d8a3db91f066ab
Permisos Básicos en Linux: https://www.profesionalreview.com/2017/01/28/permisos-basicos-linux-ubuntu-chmod/

El primer carácter de los archivos puede ser el siguiente:

Permiso   Identifica
	*.*           Archivo
	*d*           Directorio
	*b*           Archivo de bloques especiales (Archivos especiales de dispositivo)
	*c*           Archivo de caracteres especiales (Dispositivo tty, impresora...)
	*l*            Archivo de vinculo o enlace (soft/symbolic link)
	*p*          Archivo especial de cauce (pipe o tubería) 

Permiso    Identifica
	*-*           Sin permiso
	*r*           Permiso de lectura
	*w*          Permiso de escritura
	*x*           Permiso de ejecución

**>**= Redirigirá hacia algún fichero.
**>>**= Redirigirá hacia algún fichero haciendo append.
~~~ Ejemplo
- echo "Hola probando" > file.txt
- echo "Buenos días" >> file.txt
- cat file.txt
--------------------------------
       │ File: file.txt
───────┼──────────────────────────────────────────────────────────────────────
   1   │ Hola probando
   2   │ Buenos días
--------------------------------

- ls -al
drwxr-xr-x s4njer s4njer 4.0 KB Sun Jan  8 16:52:18 2023  .
drwxr-xr-x s4njer s4njer 4.0 KB Sat Jan  7 17:48:04 2023  ..
.rw-r--r-- s4njer s4njer  19 B  Sun Jan  8 16:52:10 2023  file.txt
~~~

Tipo       Usuario   Grupo   Otros  Nombre del archivo
  .               rw-        r--        r--          file.txt
***
#### Asignación de permisos
Enlaces:

- Asignación de permisos: [https://www.ionos.es/digitalguide/servidores/know-how/asignacion-de-permisos-de-acceso-con-chmod/](https://www.ionos.es/digitalguide/servidores/know-how/asignacion-de-permisos-de-acceso-con-chmod/)

- Propietarios y permisos: [https://atareao.es/tutorial/terminal/propietarios-y-permisos/](https://atareao.es/tutorial/terminal/propietarios-y-permisos/)

- Gestión de usuarios, grupos y permisos en Linux: [https://computernewage.com/2016/05/22/gestionar-usuarios-y-permisos-en-linux/](https://computernewage.com/2016/05/22/gestionar-usuarios-y-permisos-en-linux/)

- Gestión de usuarios y grupos en Linux: [https://atareao.es/como/gestion-de-usuarios-y-grupos-en-linux/](https://atareao.es/como/gestion-de-usuarios-y-grupos-en-linux/)

~~~
- ls -al
drwxr-xr-x s4njer s4njer 4.0 KB Sun Jan  8 16:52:18 2023  .
drwxr-xr-x s4njer s4njer 4.0 KB Sat Jan  7 17:48:04 2023  ..
drwxr-xr-x root root 0 B Fri Mar 18 12:31:39 2022  Prueba 
~~~

Podemos ver que la carpeta *Prueba* tiene como usuario a *root*, por lo que si accedemos como otro usuario solamente tendremos acceso como *read* (Lectura) y *executable* (Ejecución), por lo que no podremos hacer cambios en la carpeta.

>Tipo     Usuario    Grupo    Otros   Nombre del archivo
     d           rwx          r-x         r-x          Prueba

Para cambiar los permisos podremos usar el comando *chmod*.
~~~
- chmod o+w prueba 
- ls -al

drwxr-xr-x s4njer s4njer 4.0 KB Sun Jan  8 17:44:11 2023  .
drwxr-xr-x s4njer s4njer 4.0 KB Sat Jan  7 17:48:04 2023  ..
drwxr-xrwx root   root   4.0 KB Sun Jan  8 17:45:38 2023  prueba
~~~

- Este nos permitirá asignar a los usuarios *others* el permiso de *escritura* (w) en la carpeta *'prueba'*.
Podemos cambiar los grupos a los que pertenece el archivo con *chgrp* (change group).

~~~
- chgrp s4njer prueba
- ls -al

drwxr-xr-x s4njer s4njer 4.0 KB Sun Jan  8 17:45:38 2023  .
drwxr-xr-x s4njer s4njer 4.0 KB Sat Jan  7 17:48:04 2023  ..
drwxr-xrwx root   s4njer 4.0 KB Sun Jan  8 17:45:38 2023  prueba
~~~

La opción '*-*' quitará los permisos que hayamos asignado.
	
~~~
- chmod u-x, g-rx, o+w,o-x prueba
~~~

- **useradd** = Agrega un usuario
	-s = Tipo de shell que va a usar
	-d = El directorio principal
	
~~~
useradd pepe -s /bin/bash -d /home/pepe
~~~

- **chown** = Permite cambiar el propietario que pertenece el archivo, directorio, etc..
	
~~~
- cd /home
- chown pepe pepe
- ls -l /home

drwxr-xr-x pepe s4njer 4.0 KB Sun Jan  8 17:45:38 2023  pepe 
drwxr-xr-x root   s4njer 4.0 KB Sun Jan  8 17:45:38 2023  s4njer

- chgrp pepe pepe
- ls -l /home
drwxr-xr-x pepe pepe 4.0 KB Sun Jan  8 17:45:38 2023  pepe 
drwxr-xr-x root   s4njer 4.0 KB Sun Jan  8 17:45:38 2023  s4njer
~~~

Esto nos permitirá cambiar tanto el propietario *s4njer* como el grupo *root* en el directorio *pepe*, siendo mucho más rápido en cambiar tanto el propietario como al grupo que pertenece.
	
~~~
chown s4njer:root pepe
~~~

Podríamos también agregar al mismo grupo (nuevo) dos usuarios, para ello usaremos los comandos "*groupadd new_grupo*" y "*usermod -a -G
	
~~~
- groupadd circulo
- usermod -a -G circulo user
~~~

Para ver el cambio podremos mirar con:
	
~~~
cat /etc/group | grep new_grupo
~~~

***
#### Notación Octal de permisos
Enlaces:

- Permisos del sistema de archios GNU/Linux:[https://blog.alcancelibre.org/staticpages/index.php/permisos-sistema-de-archivos](https://blog.alcancelibre.org/staticpages/index.php/permisos-sistema-de-archivos)
- Los permisos de UNIX, Linux y Mac OS: [https://www.prored.es/los-permisos-de-unix-linux-y-mac-os/](https://www.prored.es/los-permisos-de-unix-linux-y-mac-os/)

Para calcular los octales debemos saber que los permisos son potencias de dos.
(2^0 = Ejecución, 2^1 = Escritura, 2^2 = Lectura)
~~~
2^0 = 1 -> Acceso    (x)
2^1 = 2 -> Escritura (w)
2^2 = 4 -> Lectura   (r)
	rwx r-x -wx
	421 401 021
	 7   5   3
~~~

![[Valor-octal.png]]
![[Valor-video-s4vitar.png]]
***
#### Permisos especiales Sticky Bit
Enlaces:

- Sticky Bit - Otro Permiso especial: [https://blog.carreralinux.com.ar/2016/10/sticky-bit-permiso-especial/](https://blog.carreralinux.com.ar/2016/10/sticky-bit-permiso-especial/)
- El bit Sticky | Tutorial de GNU/Linux: [https://www.fpgenred.es/GNU-Linux/el_bit_sticky.html](https://www.fpgenred.es/GNU-Linux/el_bit_sticky.html)

 El *Sticky bit* significa que un usuario sólo podrá modificar y eliminar archivos y directorios subordinados dentro de un directorio que le pertenezca.

Se representa mediante una "*t*" en la máscara de permisos, apareciendo en la posición del permiso de búsqueda de los otros. Se aplica a directiorios de uso público, es decir, aquellos que tienen todos los permisos activados y por tanto, todo el mundo puede crear y borrar ficheros.

~~~
Aunque el archivo dentro de un directorio tenga todos los permisos no significa que vayas a poder tocarlos, ya que al estar el *Sticky Bit* en el directorio padre sólamente podrá ser modificado por el propietario.
~~~

Esto puede ser un poco peligroso porque un usuario puede borrar los ficheros de otros y por eso se usa el *Sticky Bit*. Con este permiso se consigue que cada usuario solo pueda borrar los ficheros que son de su propiedad.

>  Un directorio al que se le suele activar el sticky bit es /tmp

![[Sticky-Bit-Ejemplo.png]]
**¿Cómo podemos agregarlo a cualquier carpeta?**
~~~
**Octal:**              | **Asignación manual:**
------------------------|---------------------------
chmod 1xxx directorio/  | chmod o+t directorio/
~~~

***
#### Control de atributos de ficheros en Linux - Chattr y Lsattr
Enlace:

- Control de atributos de ficheros Linux: [Chattr y Lsattr](https://rm-rf.es/chattr-y-lsattr-visualizar-y-modificar-atributos-en-sistemas-de-ficheros-linux/#:~:text=El%20primer%20comando%2C%20lsattr%20permite,chmod%2C%20chown%2Csetfacl%E2%80%A6)
- Comandos Chattr y Lsattr en Linux: [https://programmerclick.com/article/5604675172/](https://programmerclick.com/article/5604675172/)

**Lsattr**: Nos servirá para listar atributos de ficheros y directorios (es una especie de ls pero viendo los atributos más avanzados.).
~~~ bash
- lsattr -a /home/hack4u
--------------e------- ./configuration
--------------e------- ./hola
--------------e------- ./test
~~~

**Chattr**: Nos permitirá asignar atributos a ficheros y directorios, las letras que pasamos como parámetro simbolizan los atributos a modificar y el (+) o el (-) si lo añadimos o quitamos.
~~~ bash
- chattr +i /home/hack4u/test/prueba
- lsattr /home/hack4u/test/
----i---------e------- ./hola
~~~

**Parámetros:**
>Deshabilitar la modificación de acceso al fichero (atime) : --------- A
  Comprimir automáticamente el fichero en el disco: ----------------- c
  Bloquear la modificación o borrado de un archivo: ------------------ i
  Permitir recuperación de archivo aunque sea eliminado: ------------ u
  Al eliminar un archivo, sobreescribir con 0 todos sus bloques: ------ e
  Escribir de forma síncrona a disco cambios en los ficheros: --------- S

***
#### Permisos especiales - SUID y SGID

- Permisos SGID, SUID y Sticky Bit:[https://deephacking.tech/permisos-sgid-suid-y-sticky-bit-linux/#:~:text=Permiso%20SGID,-El%20permiso%20SGID&text=Si%20se%20establece%20en%20un,perteneciente%2C%20el%20grupo%20del%20directorio.](https://deephacking.tech/permisos-sgid-suid-y-sticky-bit-linux/#:~:text=Permiso%20SGID,-El%20permiso%20SGID&text=Si%20se%20establece%20en%20un,perteneciente%2C%20el%20grupo%20del%20directorio.)
- Permisos especiales en Linux:[https://www.ochobitshacenunbyte.com/2019/06/17/permisos-especiales-en-linux-sticky-bit-suid-y-sgid/](https://www.ochobitshacenunbyte.com/2019/06/17/permisos-especiales-en-linux-sticky-bit-suid-y-sgid/)
- Los bits SUID, SGID y Sticky:[https://www.ibiblio.org/pub/linux/docs/LuCaS/Manuales-LuCAS/SEGUNIX/unixsec-2.1-html/node56.html](https://www.ibiblio.org/pub/linux/docs/LuCaS/Manuales-LuCAS/SEGUNIX/unixsec-2.1-html/node56.html)

-   **Sticky bit:** 1000.
-   **SGID:** 2000.
-   **SUID:** 4000.

- **SUID**: Cuando un binario sea SUID, seas el usuario que seas podrás ejecutar el fichero o binario de forma temporal.

- **GUID**: Es lo mismo que lo anterior pero para los grupos. 

> Si conseguimos que su python3.x tenga el permiso SUID, sólamente deberemos:
> 
> - import os = Esto importará los paquetes necesarios
> - os.setuid(0) = Al seleccionar el uid como 0 tendremos acceso root
> - os.system("bash") = Nos proporcionará una bash terminal.

La forma en la que nosotros agregaremos un SUID y el GUID será a través del comando el cual nos permite asignar-deasignar permisos, el comando **CHOWN** y en los ejemplos siguientes lo estaré agregando en notación octal.

![[SUID-GUID_which-python.png]]
~~~ bash
chmod 4775 /usr/bin/python3.10
which python3.10 | xargs ls -l

-rwsr-xr-x 1 root root 14272 dic 19 18:35 /usr/bin/python3.10
~~~

> Hoy día 17 de enero de 2023 se conoce una vulnerabilidad en el binario *pkexec* (/usr/bin/pkexec), el cual permite escalar privilegios.

Con el siguiente comando puedes encontrar todos los SUID y GUID de tu sistema desde la raíz:
~~~ bash
* SUID:
find / -type f -perm -4000
* GUID:
find / -type f -perm -2000
~~~

*** 

#### Cuestionario de permisos (82%)
Atendiendo a los permisos representados y siendo el usuario Juan, ¿podré borrar el archivo?
~~~ bash
[juan@hack4u ejercicios]$ ls -l
total 0
-rwx-w---x 1 s4vitar lammer 0 Jun 21 14:49 file

[juan@hack4u ejercicios]$ id
uid=1001(juan) gid=1002(juan) groups=1002(juan), 1001(lammer)
~~~
- No.
> **Juan no podrá borrar el archivo. El usuario Juan forma parte del grupo 'lammer' y atendiendo a los permisos, los miembros de este grupo pueden alterar el contenido de este archivo, pero no borrarlo. En este caso sólo el propietario podría borrar el archivo, el usuario Juan no tiene privilegios para esto.**

Representa los siguientes permisos en octal
![[Representacionlinux-octa.png]]

¿Podré borrar el siguiente archivo con los siguientes permisos configurados?
~~~ bash
> ls -l
.-w------- s4vitar s4vitar 0 B Tue Jun 21 14:49:57 2022 file

> whoami
s4vitar
~~~
- **Sí.**
>Se podrá borrar el archivo dado ya que yo soy el propietario y tengo permisos de escritura.

 Atendiendo a los permisos representados y siendo el usuario Juan, ¿Podré borrar el archivo?
~~~ bash 
[juan@hack4u ejercicios]$ ls
file

[juan@hack4u ejercicios]$ ls -l
total 4
-rwx-w---x 1 s4vitar lammer 5 Jun 21 15:50 file

[juan@hack4u ejercicios]$ id
uid)1001(juan) gid=1002(juan) groups=1002(juan), 1001(lammer)

[juan@hack4u ejercicios]$ ls -l ../ | grep ejercicios
drwxr-xr-x 2 juan juan 4096 Jun 21 14:49 ejercicios
~~~
>Sí, ya que el factor que hace que en este caso el usuario Juan pueda borrar el archivo es que este reside en un directorio cuyo propietario es el mismo usuario que pretende borrar el archivo (Juan). Este factor es determinante.

 Representa en octal el permiso final del que dispondrá el archivo
~~~ bash
ls -l
.r--rw-r-- s4vitar s4vitar 0 B Tue Jun 21 14:49:5 2022 file
[root@hack4u]$ chmod o+w,g-w,u+x file
~~~
- **546**
>4+1 para el propietario, 4 para los grupos y 4+2 para otros.

Representa los siguientes permisos de octal al sistema de representación de permisos en Linux.
![[Octal-Representacionlinux.png]]

¿Qué pasará cuando ejecute el sigiente comando?
~~~ bash
ls -l
.--------x s4vitar s4vitar 0 B Tue Jun 21 14:49:57 2022 file
whoami
s4vitar
~~~
- **No se podrá almacenar el output del comando '*whoami*' en el archivo file, dado que pese a ser propietario, el permiso asignado en el archivo prevalece.**
> Prevalece el permiso asignado al archivo, por lo que de tratar almacenar el output del comando en el archivo 'file' obtendremos un error de tipo 'Permission Denied'.

¿De qué formas podría conseguir que el archivo '*file*' tuviera los permisos finalmente representados?
~~~ bash
ls -l
.r-xr--rw- s4vitr s4vitar 0 B Tue Jun 21 14:49:57 2022 file
ls -l
.rwxr----x s4vitar s4vitar 0 B Tue Jun 21 14:49:57 2022 file
~~~
- **chmod 741 file**
> El primer valor '7' corresponde al privilegio para el propietario, donde se indica que el propietario puede leer, escribir y ejecutar el archivo. El segundo valor '4' corresponde al privilegio para el grupo', donde se indica que los grupos sólo pueden leer. Por último, el valor '1' corresponde al privilegio de 'otros', donde se indica que otros pueden ejecutar el archivo.

-  **chmod u+w,o-rw,o+x file**
> En esta instrucción, por un lado estaríamos indicando que el propietario queremos que pueda escribir sobre el archivo. Por otro lado, estamos retocando 2 veces los privilegios asignados para 'otros'. De primeras, le estamos retirando los permisos de lectura y escritura. Posteriormente, le incorporamos el privilegio de ejecución.

-  **chmod u+w,g+w,o-rw,g-wx,o+x file**
> En una misma línea puedes añadir y quitar permisos para un mismo grupo múltiples veces. También puedes indicar que quieres quitar un permiso el cual ya está previamente quitado para un grupo dado, esto no creará ningún tipo de conflicto.

¿Qué pasará cuando se ejecute el último comando indicado?
~~~ bash
> ls -l
.rwxrwxrwx s4vitar s4vitar 0 B Tue Jun 21 14:49:57 2022 file
> lsattr
----i---------e------- ./file
> whoami
root
~~~
- **No se podrá borrar el archivo, dado que cuenta con la flag inmutable**
> Anteriormente se le ha aplicado al archivo el permiso avanzado 'chattr +i file', de forma que se le queda asignada una flag inmutable que hace que ni el usuario root pueda borrarlo.

¿Qué permiso en octal tendré que asignar para lograr que el archivo cuente con los permisos indicados?
~~~ bash
ls -l
.rw--w-r-x s4vitar s4vitar 0 B Tue Jun 21 14:49:57 2022 file
~~~
- El permiso a asignar en octal será el *625*, de forma que el propietario podrá *leer* y *escribir*, los grupos podrán *escribir* y otros podrán tanto *leer* como *acceder* al archivo.

¿Qué ha pasado aquí?
~~~ bash
ls -l
drwxr-xr-x 1001 1002 4.0 KB Tue Jun 21 15:52:41 2022 Ejercicios
~~~
- **El directorio en cuestión tenía un propietario y grupo asignado los cuales han sido eliminados a nivel de sistema.**
> En su momento al directorio se le asignó un propietario y un grupo, pero posteriormente con los comandos 'userdel' y 'groupdel' se borraron tanto el propietario como el grupo existente a nivel de sistema.

#### Privilegios Especiales - Capabilities
- Linux Kernel Capabilities (No solo de sudo vive root): [https://www.incibe-cert.es/blog/linux-capabilities](https://www.incibe-cert.es/blog/linux-capabilities)
- ¿Qué son las Linux Capabilities?: [https://www.etl.it.uc3m.es/Linux_Capabilities](https://www.etl.it.uc3m.es/Linux_Capabilities)
Las capabilities son privilegios que se le asigna a un binario.
Por ejemplo, hay comandos en python el cual nos permite asignar nuestra *UID* a 0, osea te convertirá en *root*:
~~~ python
# Esto en el caso del ejecutable de python3
import os
os.setuid(0)
os.system("whoami")

'root 0'
~~~

- **getcap** -> Lista capabilities
- **setcap** -> Asigna capabilities

~~~ bash
getcap -r / 2>/dev/null 
~~~
 >Esto nos permitirá listar todas las capabilities de nuestro sistema de forma recursiva desde la raíz y enviando los errores (de ahí el número 2) a /dev/null (Descriptor de archivo).

~~~ bash
setcap cap_setuid+ep /usr/bin/python3.9
~~~
> Esto nos permitirá asignar al binario *python3.9* la opción de cambiar el UID, por lo que podrá cambiar de usuarios cuando quiera. 

---
