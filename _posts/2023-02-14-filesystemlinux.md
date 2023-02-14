---
layout: post
title: 'Linux FileSystem /'
date: 2023-02-14
categories: filesystem
img_path: /assets/images/
---
# Estructura de directorios del sistema (FileSystem de Linux)
A modo de sumario, dejo la descripción de cada ruta del sistema:
![linuxfilesystem](linuxfilesystem.png.png)
**/ ->**  Es el directorio principal a partir del cual se ramifican todo el resto de directorios.

**/bin ->** Es un directorio estático y compartible en el que se almacenan archivos binarios/ejecutables y necesarios para el funcionamiento del sistema *(Casi todos los usuarios pueden usarlos)*, mientras que el directorio */sbin* sería exclusivamente lo mismo pero para el usuario root.

**/boot ->** Estático y no compartible que contiene la totalidad de archivos necesario para el arranque del ordenador excepto los archivos de configuración. *(Algunos de los archivos indispensables para el arranque del sistema que acostumbra almacenar este directoriio son el kernel y Grub)*

**/dev ->** El SO Gnu-Linux trata los dispositivos de hardware como si fueran un archivo.
- *cdrom* = CDROM
- *sda* = SATA
- *audio* = Nuestra tarjeta de sonido.
- *psaux* = Puerto PS/2
- *lpx* = impresora.
- *fd0* =  Nuestra disquetera. 

**/etc ->** Es un directorio estático y contiene los archivos de configuración del sistema operativo, y también el de diversos programas. *(Algunos archivos de configuración de esta carpeta pueden ser sustituidos o complementados por archivos de configuración ubicados en nuestra carpeta personal /home)*

**/home ->** Es un directorio variable y compartible. Está destinado a alojar la totalidad de archivos personales de los distintos usuaros del sistema operativo a excepción del usuario root. *Algunos archivos almacenados en esta carpeta son fotografías, documentos, vídeos, descargas, etc...*

**/lib ->** Es un directorio estático y que puede ser compartible. Este directorio contiene bibliotecas compartidas que son necesarias para arrancar los ejecutables que se almacenan en los directorios */bin* y * */sbin*

**/mnt ->** Tiene la finalidad de albergar puntos de montaje de los distintos dispositivos de almacenamiento.

**/media ->** Es similar a la del directorio /mnt. Contiene los puntos de montaje de los medios extraíbles de almacenamiento *(Memorias USB, lectores CD-ROM, Unidades de disquete, etc..)*

**/opt ->** Es estático y compartible. Almacena programas que no vienen con nuestro sistema operativo como por ejemplo Spotify, Google-earth, Google Chrome, TeamViewer, etc...

**/proc ->** Se trata de un sistema de archivos virtual. Nos proporciona la información acerca de los distintos procesos y aplicaciones que se están ejecutando en nuestro sistema operativo.

**/sys ->** Directorio que contiene información similar a la del directorio /proc. Dentro de esta carpeta podemos encontrar información estructurada y jerárquica acerca del kernel de nuestro equipo, de nuestras particiones y sistemas de archivo, de nuestros drivers, etc...

**/root ->** Se trata de un directorio variable no compartible. Este directorio es el directorio /home del administrador del sistema *(usuario root)*

**/srv ->** Se usa para almacenar directorios y datos que usan ciertos servidores que podamos tener instalados en nuestro ordenador.

**/tmp ->** Es donde se crean y almacenan los archivos temporales y las variables para que los programas puedan funcionar de forma adecuada.

**/usr ->** Es un directorio compartido y estático. Contiene la gran mayoría de programas instalados en nuestro sistema operativo. *(Todo el contenido almacenado en la carpeta /usr es accesible para todos los usuarios y su contenido es solo de lectura)*

**/var ->** Contiene archivos de datos variables y temporales como por ejemplo los registros del sistema (logs), los registros de programas que tenemos instalados en el sistema operativo, archivos spool, etc. *(Su principal función es la de detectar problemas y solucionarlos. Se recomienda ubicar este directorio en una partición propia, y en caso de no ser posible es recomendable ubicarlo fuera de la partición raíz)*

**/lost-found ->** Se crea en las particiones de disco con un sistema de archivos ext, los cuales usando herramientas podríamos recuperar nuestro sistema operativo *(por ejemplo, fsch)*. 
- Si nuestro sistema no ha presentado problemas este directorio estará completamente vacío y, en el caso de haber problemas éste contendrá ficheros y directorios que han sido recuperados tras la caída del sistema operativo.

## Uso de bashrc y zshrc
- ¿Qué es Bashrc en Linux? [https://www.compuhoy.com/que-es-bashrc-en-linux/](https://www.compuhoy.com/que-es-bashrc-en-linux/)
- ¿Por qué deberías usar ZSH? [https://respontodo.com/que-es-zsh-y-por-que-deberia-usarlo-en-lugar-de-bash/](https://respontodo.com/que-es-zsh-y-por-que-deberia-usarlo-en-lugar-de-bash/)

ZSH, también llamado Z shell, es una versión extendida de Bourne Shell (sh), con muchas características y soporte para complementos y temas. Dado que se basa en el mismo shell que Bash, ZSH tiene muchas de las mismas características, y cambiar es muy sencillo.

ZSH tiene demasiadas funciones para enumerarlas aquí, algunas mejoras menores en Bash, pero estas son algunas de las principales:
- **CD Automático:**
- **Expansión de ruta recursiva:** por ejemplo, <</u /lo /b>> se expande a <</usr /local /bin>>
- **Corrección ortográfica y finalización aproximada:** si comete un pequeño error al escribir un nombre de directorio ZSH te lo autocompletará.
- **Compatibilidad con complementos y temas:** ZSH incluye muchos marcos de complementos diferentes.
- 

  ~~~ bash
	# Esto nos mostrará "Tu IP privada es:" y buscará dentro de nuestro nombre de host las interfaces de red, y con awk mostraremos sólamente el primer campo que nos aparece.
echo "Tu IP privada es: $(hostname -I | awk '{print $1}')"
  ~~~

#### Actualización y upgradeo del sistema
Actualizar nuestro sistema nos sirve para actualizar tanto archivos del sistema como binarios, librerías, etc....
> En Parrot Security OS *no* se debe usar la función  **apt upgrade** para actualizar archivos del sistema, lo que se debe hacer en este caso es usar el comando **parrot-upgrade**, ya que actualizará todo el sistema sin dañar archivos importantes de este sistema.

Por lo que se debe tocar así sólamente:
- **Arch Linux:** pacman -Syu
-  **GNU/Linux:** apt upgrade

#### Búsquedas a nivel de sistema
- Comandos Find y Locate en Linux: [https://www.hostinger.es/tutoriales/como-usar-comando-find-locate-en-linux/](https://www.hostinger.es/tutoriales/como-usar-comando-find-locate-en-linux/)

Esto nos servirá mucho para detectar archivos que pertenezcan al grupo y usuario que queramos y demás.

El siguiente comando buscará archivos desde el directorio raíz (/) con el nombre `passwd` y enviándolo al /dev/null con el identificador 2 (que es el de sterr) para que no nos aparezcan errores 2>/dev/null

~~~ bash
find / -name passwd 2>/dev/null
~~~

Este hará lo mismo pero sumándole el comand ls con su flag -l (para ver propiedades de los archivos), mientras que el segundo comando buscará todos los SUID desde la raíz del sistema.
~~~ bash
find / -name passwd 2>/dev/null | xargs ls -l
find / -perm -4000 2>/dev/null
~~~

Para buscar por grupos símplemente debemos asignar la flag `-group` y nuestro nombre de grupo deseado:
~~~ bash
find / -group wheel 2>/dev/null
~~~

Buscará dentro del usuario `root` todo lo que sea de escritura, mientras que en el segundo, veremos que buscará desde `root` -files que sean ejecutables, enviándolo al stderr para no mostrar errores.
~~~ bash
find / -user root -writable 2>/dev/null
find / -user root -executable 2>/dev/null -type f
~~~

Si alguna vez nos acordamos de una parte de un archivo pero no sabemos dónde está guardad ni si lo tenemos, es tan fácil como esto:
> Supondremos que estamos buscando el ejecutable *dexdump.sh*

~~~ bash
find / -name dex\* 2>/dev/null
find / -name \*exdum\* 2>/dev/null
find / -name dex\*.sh 2>/dev/null
~~~

---

## Creación de Scripts en bash
Por ahora no estaremos viendo al 100% bash, ya que eso lo tocaremos más adelante para crearnos una serie de herramientas que nos servirán para practicar y entender varios de los comados existentes.

> El comando *seq 1 20* creará un bucle del 1 al 20
~~~ bash
for i in $(seq 1 20); do echo "[+] Iteración número $i"; done
~~~

La mejor manera para aprender cómo funciona este lenguaje de comandos es tratar de permanecer el mayor tiempo posible desde consola. Primero debemos aprender una serie de comandos básicos (en internet cuentas con muchas Cheat sheets), pero después de tener esta base bien cubierta debemos tratar de hacerlo todo con la consola, ya que es la mejor manera de aprender y adquirir soltura con este lenguaje.

**Ejemplos sencillos:**
Esta es nuestra interfaz de red enp2s0:
~~~ bash
2: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.1.3/24 brd 192.168.1.255 scope global dynamic noprefixroute enp2s0
~~~
Y aquí simplemente nos mostrará :
> Nos mostrará todas la interfaces *>* Filtrará enp2s0 que es nuestra interfaz de red *>* Desde abajo (tail) nos mostrará la primera línea *>* Elegiremos el segundo parámetro que nos aparece en la cola, que es nuestra dirección IP y nuestro broadcast *>* cortamos el delimitador '/' y escogemos el primer field para quitar el broadcast..
~~~ bash
ip a | grep enp2s0 | tail -n 1  | awk '{print $2}' | cut -d '/' -f 1
~~~

Este también nos hará lo mismo:
~~~ bash
ip a | grep enp2s0 | tail -n 1  | awk '{print $2}' | tr '/' ' ' | awk '{print $1}'
~~~

Dando como resultado símplemente nuestra dirección ip: *192.168.1.3*, ahora símplemente crearemos un *archivo.sh* para automatizarlo:
~~~ bash
#!/bin/bash
echo -e "\n[+] Esta es tu dirección IP privada -> $(ip a | grep enp2s0 | tail -n 1  | awk '{print $2}' | cut -d '/' -f 1)\n"
~~~
> *Echo* nos mostrará por pantalla todo lo que esté entre comillas, y el parámetro *-e* nos servirá para incluir carácteres especiales, *\n*, por ejemplo es uno de ellos, que nos permitirá crear un salto de línea tanto al principio como al final del script.

También podremos agregar colores tal que así (se crean directamente las variables):
~~~ bash
#!/bin/bash

#Colours
greenColour="\e[0;32m\033[1m"
endColour="\033[0m\e[0m"
redColour="\e[0;31m\033[1m"
blueColour="\e[0;34m\033[1m"
yellowColour="\e[0;33m\033[1m"

echo -e "\n${yellowColour}[+]${endColour} ${blueColour}Esta es tu dirección IP privada -> ${endColour} ${redColour}$(ip a | grep enp2s0 | tail -n 1  | awk '{print $2}' | cut -d '/' -f 1)${endColour}\n"
~~~
Y este sería el resultado:
![bashscript1](bashscript1.png)
