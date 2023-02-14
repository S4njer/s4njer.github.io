---
layout: post
title: 'SSH - bandit.overthewire.org'
date: 2023-02-14
categories: [bandit, overthewire, ssh, búsqueda de archivos]
img_path: /assets/images/
---

![Overthewire](overthewire.png)

# Conexiones SSH - Bandit.overthewire.org
SSH es el nombre de protocolo y del programa que lo implementa cuya principal función es el acceso remoto a un servidor poor medio de un canal seguro en el que toda la información está cifrada.

Esto te puede servir para practicar *Bash Scripting* y empezar a saber como funciona el *file system* de Linux. 

Puedes practicar uniéndote al siguiente servidor remoto:
~~~ bash
# La contraseña para el usuario bandit0 es bandit0
ssh bandit0@bandit.labs.overthewire.org -p 2220
~~~

Si tenemos una terminal diferente y queremos tener otra configuración:
~~~ bash
# Permite ver que terminal tenemos
echo $TERM

# Usar otra terminal
export TERM=nombre_terminal
~~~

## Lectura de archivos especiales
Puede ser que en alguna ocasión te encuentres con algún archivo que empiece por el símbolo *'-'*

En tal caso, recuerda que a la hora de tratar de mostrar el contenido que este tenga, no podremos hacerlo de la forma tradicional, pues al contener guiones es probable que el sistema lo interprete como parámetros del comando que estés primeramente definiendo.

Para ello hay muchas maneras, una de ellas es escribiendo la ruta absoluta o de las siguienets maneras:

> Ruta absoluta -> *cat /home/usuario/-*
> Ruta relativa (directorio actual)-> *cat ./-*
> 
> A través de script  (hará un cat desde el directorio que estés a -)-> *cat $(pwd)/-*
> 
> Buscará de forma recursiva todas las "words" en el directorio actual y enviándolo al stderr (nos aparecerá el resultado debajo de todo, por lo que podríamos concatenar con un *tail -n 1*, si es al revés simplemente el tail lo cambiamos por un head a través de una pipe *|* ):
> 	*grep -r "\\w" 2>/dev/null*
> 
> Hará lo mismo pero mostrando el segundo campo y tomando como delimitador el caracter -> ":"
> *El FS=":"* nos permite quitar esos dos puntos y podamos dividir el mismo parámetro.
~~~ bash
grep -r "\w" 2>/dev/null | tail -n 1 | awk '{print $2}' FS=":"
~~~
>
 Con el comando cut
~~~ bash
grep -r "\w" 2>/dev/null | tail -n 1 | cut -d ':' -f 2
~~~
>
>Con el comando *tr* (cambiará el primer dato por el segundo) y printando el último argumento:
~~~ bash
grep -r "\w" 2>/dev/null | tail -n 1 | tr ':' ' ' | awk 'NF{print $NF}'
~~~

Podríamos también añadirle un texto más la contraseña  que estará dentro del parámetro *$*, aunque esta opción es por jugar un poco:
~~~ bash
echo -e "\n[+] La contraseña es $(grep -r "\w" 2>/dev/null | tail -n 1 | awk '{print $2}' FS=":")"
~~~

Para referirte a archivos con espacios debes escapar los espacios, o referirte a estos con los espacios pero haciendo uso de comillas dobles.

Esto es así debido a la forma que tiene *Bash* de interpretar los archivos a la hora de aplicarles un comando. En caso de referirte a un archivo con espacios, si no escapas o encasillas la cadena entre comillas dobles se pensará que le estás diciendo que quieres aplicar una acción sobre una serie de archivos que le indicas.

Para ello deberíamos usar mucho el *tab <- ->*, ya que autocompletará muchas partes.
- **Comillas y slash invertido**:
~~~ bash
cat "spaces in this filename"
cat spaces\ in\ this\ filename
~~~~

## Directorios y archivos ocultos
Todo lo que empieza por *'.'* es oculto en el sistema. Los archivos ocultos (también llamados archivos dot) en los sistemas operativos Unix, son archivos que se utilizan para ejecutar ciertos scripts o para almacenar la configuración de algunos servicios del host.
Esto no tiene por qué ser del todo así, dado que también puedes crearte tus propios archivos y directorios ocultos.

Buscará todos los *. (dots)*  desde el directorio actual de tipo file, fltrándolo con *grep* haciendo verbose y la E, la cual nos permite buscar múltiples ficheros (aquí abajo son bashrc, profile y logout), agregándole al output con xargs el comando cat para que nos lo muestre. 
~~~ bash
find . -type f | grep -vE "bashrc|profile|logout" | xargs cat
~~~

## Detección del tipo y formato de archivos
Muchos formatos de archivo no están pensados para leerse como text. Si dicho archivo se ve accidentalmente como un archivo de texto, su contenido será ininteligible. Sin embargo, a veces la firma del archivo se puede reconocer cuando se interpreta como texto.

Por aquí dejo una lista de firmas de archivos. La columna correspondiente a *Firma Hexadecimal* está directamente relacionada a los primeros bytes que en comparación a los primeros bytes del archivo con el que estamos tratando nos permitirán saber el tipo de formato al que nos estamos enfrentando:

- Lista de firmas de archivos: [https://en.wikipedia.org/wiki/List_of_file_signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)

Buscamos directorios ocultos de tipo file y grepeamos la palabra *\-file*, concatenándolo con el comando file para que  nos muestre qué formato de archivo es:
~~~ bash
find . -type f | grep "\-file" | xargs file
~~~

Aquí podemos apreciar que solamente un file es de tipo **ASCII text**, por lo que la contraseña seguramente esté ahí.
![formatoarchivos](formatoarchivos.png)

## Búsquedas precisas de archivos
- ¿Cómo buscar y encontrar archivos en Linux?: [https://www.ionos.es/digitalguide/servidores/configuracion/comando-linux-find/](https://www.ionos.es/digitalguide/servidores/configuracion/comando-linux-find/)

Buscará desde tu directorio actual en todos las carpetas ocultas archivos y no buscará ejecutables (de ahí el *!*) de tamaño 1033 bytes (en este caso la c se corresponde a bytes), también veremos el formato del archivo:
~~~ bash
find . -type f ! -executable -size 1033c | xargs file
~~~
![busquedaprecisa](busquedaprecisa.png)

Para buscar por usuarios (bandit7) y grupos (bandit6) con un tamaño de 33 bytes podríamos usar lo siguiente:
~~~ bash
find . -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null | xargs cat
~~~

Con esto ya sabrás un poco sobre como aplicar búsquedas a nivel de sistema para encontrar aquellos archivos cuyas propiedades específicas recuerdes. 

## Métodos de filtrado de datos
- AWK Cheat Sheet: [https://www.shortcutfoo.com/app/dojos/awk/cheatsheet](https://www.shortcutfoo.com/app/dojos/awk/cheatsheet)
- AWK Cheat Sheet 2:[https://bl831.als.lbl.gov/~gmeigs/scripting_help/awk_cheat_sheet.pdf](https://bl831.als.lbl.gov/~gmeigs/scripting_help/awk_cheat_sheet.pdf)
También dejo un enlace de interés por si quieres indagar un poco más acerca del uso del comando *cut*:
- Cheat Sheet: Cutting text with cut: [https://bencane.com/2012/10/22/cheat-sheet-cutting-text-with-cut/](https://bencane.com/2012/10/22/cheat-sheet-cutting-text-with-cut/)

Para que me muestre la flag, que está dentro de data.txt pero al lado de la palabra millionth:
~~~ bash
cat data.txt | grep "millionth" | cut -d ' ' -f 1
~~~

Lo podríamos concatenar con bash scripting creando un array con el valor *i* desde el 1 hasta el 50 para que busque con nuestros parámetros asignados.
~~~ bash
for i in $(seq 1 50); do echo -e "Probando con $i:"; cat example | cut -d ' ' -f $i; done
~~~
No aconsejo usar el comando *cut*, sino con el comando  *awk*:
~~~ bash
cat example | awk '{print $2}'
~~~

Para cambiar una palabra por otra como en VIM, símplemente escribimos lo siguiente usando el comando *sed* más la *s* de sustitución, para aplicarlo a toda la línea y no cambiar símplemente la primera palabra que encuentre colocamos una *g* al final :
~~~ bash
echo "Hola esto es una prueba" | sed 's/prueba/probando/g'
~~~

- Tutorial de uso de SORT: [https://www.ibidemgroup.com/edu/tutorial-sort-linux-unix/](https://victorhckinthefreeworld.com/2021/10/21/el-comando-uniq-de-gnu/)
- Tutorial de uso de UNIQ: [https://victorhckinthefreeworld.com/2021/10/21/el-comando-uniq-de-gnu/](https://victorhckinthefreeworld.com/2021/10/21/el-comando-uniq-de-gnu/)

Para bandit 8 necesitaremos encontrar la contraseña que está en *data.txt* y está en la única línea de texto que aparece una sola vez:
> - El comando sort nos aplicará un ordenamiento alfabético de todas las líneas de la A a la Z. (Este comando debemos usarlo, sino no funcionará el siguiente comando que nos aparece
> - )
> -  El comando uniq junto a la flag -u nos mostrará la única línea que es única 
{: .prompt-tip}

~~~ bash
sort data.txt | uniq -u
~~~

## Interpretación de archivos binarios
El comando **strings** de Linux permite ver los caracteres legibles para humanos dentro de cualquier archivo.
El comando **strings** no tiene nada de complicado y su uso básico es muy sencillo. Proporcionamos el nombre del archivo que deseamos que las cadenas busquen en la línea de comando y listo. Parece una tontería, pero nos puede llegar a servir de cara al futuro en ciertas circunstancias.

Usamos strings en data.txt, filtramos por *= = =*  | usamos la primera línea desde el final y printeamos el final de la línea (que es donde está el resultado)
~~~ bash
strings data.txt | grep "===" | tail -n 1  | awk 'NF{print $NF}'
~~~

## Codificación y decodificación en Base64
El algoritmo de codificación **Base64** no es un algoritmo de cifrado, se decodifica fáclmente y, por lo tanto, no debe utilziarse como un método de cifrado seguro. No recomiendo qu utilicéis esta técnica para proteger datos confidenciales, ya que se puede tensar en cuestión de segundos, en tal caso, es recomendable emplear métodos de cifrado seguros.

Como usuario bandit10, símplemente debemos descifrar con:
~~~ bash
# Forma rápida
base64 -d data.txt
# Normal
cat data.txt | base64 -d
~~~

## Cifrado César y uso de tr pra la traducción de caracteres
- Para emplear rotaciones al cifrado: [https://rot13.com](https://rot13.com)

Para los más curiosos, el cifrado **César** lleva el nombre de **Julio César**. Es uno de los tipos de cifrados más antiguos y se basa en el cifrado monoalfabético más simple. Se considrera un método débil de criptografía, ya que es fácil decodificar el mensaje debido a sus técnicas de seguridad mínimas (sólamente tiene 25 combinaciones posibles).

Aquí usamos la sustitución de palabras para el método de cifrado césar y nos dice que tiene una rotación de 13 posiciones, aquí nos da el resultado 
~~~ bash
cat data.txt | tr '[G-ZA-Fg-za-f]' '[T-ZA-St-za-s]'
cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
~~~

## Creamos un descompresor recursivo automático de archivos en Bash (b12->13)
El sistema hexadecimal es el sistema de numeración posicional que tiene como base el 16. Su uso actual está muy vinculado a la informática.

Script de la academia para esta clase, en caso de que quieras echar un ojo y tenerlo a mano:
- Script 'decompressor.sh': [https://hack4u.io/wp-content/uploads/2022/05/decompressor.sh](https://hack4u.io/wp-content/uploads/2022/05/decompressor.sh)

Para poder ver los hexadecimales de cualquier archivo símplemente debemos usar el comando *xxd*
> El segundo ejemplo de comando, nos muestra el archivo /etc/hosts *|* viendo el hexadecimal de este y mostrando sólamente estos hexadecimales *|* con xargs mostrándolo todo en una sóla línea *|* y con tr -d quitamos el delimitador que en este caso son los espacios
{: .prompt-info}
~~~ bash
cat /etc/hosts | xxd
cat /etc/hosts | xxd -ps | xargs | tr -d ' '
~~~

Para conseguir la inversa símplemente cogemos el resultado de este y decimos que nos lo muestre, agregándole de nuevo el comando *xxd -ps*  y reverse (-r)
~~~ bash
echo 23205374616e6461726420686f7374206164647265737365730a3132372e302e302e3120206c6f63616c686f7374206c6f63616c686f73742e6c6f63616c646f6d61696e2064726f706c6574312073657267696f700a3a3a3120202020202020206c6f63616c686f7374206970362d6c6f63616c686f7374206970362d6c6f6f706261636b0a666630323a3a31202020206970362d616c6c6e6f6465730a666630323a3a32202020206970362d616c6c726f75746572730a23205468697320686f737420616464726573730a3132372e302e312e31202073657267696f50206861636b34750a3139322e3136382e312e31373020716e6170616c6d756465796e650a31302e31302e31312e3138392070726563696f75732e6874620a31302e31302e31312e313836206d65746170726573732e6874620a31302e31302e31312e3138302073686f7070792e687462206d61747465726d6f73742e73686f7070792e6874620a3139322e3136382e312e3235332032736d722e6c6f63616c0a31302e3132392e39362e31343920756e6966692e6874620a | xxd -ps -r
~~~

En Linux nos puede dar problemas si al aplicar unos filtros a unos archivos o algo parecido queremos meterlo en un archivo nuevo, para ello se usa el comando *tee*, aunque para nuestro caso, usaremos el comando *sponge* (descarga el paquete moreutils) para agregarlo al mismo archivo.
No usaremos *-ps* porque queremos los hexadecimales enteros y sólamente reversearlo.
~~~ bash
cat data | xxd -r | sponge data
~~~

Posteriormente veremos qué tipo  de archivo es con el comando *file* y nos dice que **data**:
> data: gzip compressed data, was "data2.bin", last modified etc...

Lo renombramos con el comando mv a una extenxión gz quedando como *data.gz*

Usaremos el comando 7z (ya que es más intuitivo y nos sirve para todos los paquetes) y las flags que queramos:
- *7z l data* = nos permite listar el contenido del comprimido
- *7z x data* = nos permite extraer el contenido del comprimido

Al descomprimir veremos que se nos habrá creado un archivo llamado *data2.bin*, por lo que le pasaremos de nuevo el comando *file* para ver qué es y nos dice:
> data2.bin: bzip2 compressed data, block size = 900k

Al decirnos que es un archivo comprimido nos parece un poco raro, por lo que volveremos a usar con el comando *7z l data2.bin* y nos aparecerá que hay un archivo dentro llamado *data2*, por lo que ahora lo volveremos a descomprimir

Aquí nos damos cuenta que todos los archivos son comprimidos, por lo que de poco en poco iremos viendo y extrayendo aunque es un poco lento, para ello crearemos un script el cual nos lo automatice.

Creamos un archivo de bash con el nombre que queramos, yo lo tengo tal que así (arriba junto al título de esta sección se encuentra el enlace al comando completo):
~~~ bash
vim decompressor.sh
~~~

**decompresor.sh**
~~~ bash
#!/bin/bash

# Creamos una función para la parte de Captura Ctrl+C la cual nos mostrará un texto diciendo "Saliendo... y posteriormente se saldrá del programa"
function ctrl_c(){
  echo -e "\n\n[!] Saliendo...\n"
  exit 1
}
# Captura Ctrl+C
trap ctrl_c INT

# Variable para definir cuál es el archivo para realizar todo el proceso:
first_file_name="data.gz"

# Creamos una variable que nos lista el contenido de data.gz, nos muestra 3 líneas desde el final, luego la primera línea y printea el final de la línea, que es donde está el contenido del comprimido
decompressed_file_name="$(7z l data.gz | tail -n 3 | head -n 1 | awk 'NF{print $NF}')"

# Descomprimimos el nombre del resultado de la anterior variable 'decompressed_file_name' y enviamos el resultado (tanto el stderr como el stdout) para que no se nos muestre ningún error ni output.
7z x $first_file_name &>/dev/null

# Mientras haya archivos descomprimiéndose muestra Nuevo archivo descomprimido: nombre_archivo_descomprimido
while [ $decompressed_file_name ]; do
  echo -e "\n[+] Nuevo archivo descomprimido: $decompressed_file_name"
  7z x $decompressed_file_name &>/dev/null

  # Listará el decompressed_file_name enviando los errores al /dev/null:
  decompressed_file_name="$(7z l $decompressed_file_name 2>/dev/null | tail -n 3 | head -n 1 | awk 'NF{print $NF}')"
done

~~~
![bin](data9bin.png)

## Manejo de pares de claves y conexiones SSH (b13->14)
Como funcina internet en 5 minutos: [https://www.youtube.com/watch?v=7_LPdttKXPc](https://www.youtube.com/watch?v=7_LPdttKXPc)
Los pares de claves SSH son dos claves criptográficamente seguras que pueden usarse para autenticar a un cliente y un servidor SSH. Cada par de claves está compuesto por una *clave pública* y una *clave privada*.

El **cliente** mantiene la *clave privada* y debe mantenerla en absoluto secreto. Poner en riesgo la clave privada puede permitir a un atacante no autorizado iniciar sesión en los servidores que están configurados con la clave pública asociada sin autenticación adicional. Como medida de precaución adicional, es recomendable cifrar la clave en el disco con una frase de contraseña.

La *clave pública* asociada puede compartirse libremente sin ninguna consecuencia negativa. La clave pública puede usarse para cifrar mensajes que **sólo la clave privada puede descifrar**. Esta propiedad se emplea como forma de autenticación mediante el uso del par de claves.

Debemos instalarnos *OpenSSH*, para inicializar el servicio ssh símplemente escribimos *sudo systemctl start sshd*.

La localización de las claves se encuentran en */home/usuario/.ssh*

**SSH Commands:**
- *ssh-keygen* -> Generamos un par de claves pública y privada (id_rsa.pub -id_rsa )
- *ssh-copy-id -i id_rsa usuario@direccIP* -> Copiará la clave privada del usuario.
- *ssh -i id_rsa usuario@direccIP* -> usará el id_rsa para acceder al usuario.

Si tu clave pública la conviertes en claves autorizadas (lo copias como *authorized_keys*) otra persona habiendo copiado tu clave pública podrá acceder sin necesidad de contraseña, pero claramente habiendo copiado esta clave pública a su archivo /home/usuario/.ssh/authorized_keys (*atacante*).

En resumen, que si alguien copia tu clave pública o privada tiene acceso sin necesidad de contraseña a tu usuario y equipo.

## Uso de netcat para realizar conexiones (b14->15)
En total existen 65535 puertos y varios protocolos como el TCP y el UDP.
**Protocolos:**
- *TCP ->* Garantiza que el orden de los paquetes viaje en el orden correcto, que lleguen al destinatario, que no haya fragmentación o se pierdan paquetes por el camino.
- *UDP ->* Es lo contrario al TCP, ya que este no garantizará que lleguen todos los paquetes a su destinatario y pudiendo perderse unos cuantos por el camino.

Netcat es una herramienta de línea de comando que sirve para escribir y leer datos en la red. Para la transmisión de datos, Netcat uso los protocolos de red *TCP/IP y UDP*. La herramienta proviene originalmente del mundo Unix; desde entonces, se ha expandido a todas las plataformas.

Gracias a su universalidad, a Netcat se la llama **"La navaja suiza del TCP/IP"**. Puede utilizarse, por ejemplo, para diagnosticar errores y problemas qu afecten a la funcionalidad y la seguridad de una red. Netcat también puede escanear puertos, hacer streaming de datos o simplemente transferirlos. Además, permite configurar servidores de chat y de web e iniciar consultas por correo. Este software minimalista, desarrollado a mediados de los 90, puede operar en modo servidor y cliente.

Solamente como bandit14 deberemos usar este comando, e introduciendo cualquier caracter nos pedirá la contraseña actual de nuestro usuario, luego de esto, nos aparecerá una nueva contraseña.
~~~ bash
nc localhost 30000
~~~

Nos conectamos como bandit15 con la contraseña que nos han proporcionado

## Uso de conexiones encriptadas (b15->16)
- **Ncat** puede encriptar el tráfico SSL. En modo de conexión, basta con añadir la opción '*-ssl*'
~~~ bash
ncat --ssl 127.0.0.1 30001
~~~

## Creando nuestros propios escáneres en Bash (b16-17)
Dejo por aquí los 2 scripts que hemos creado para la clase:
- Script '**hostScan.sh**': [https://hack4u.io/wp-content/uploads/2022/05/hostScan.sh](https://hack4u.io/wp-content/uploads/2022/05/hostScan.sh)
- Script '**portScan.sh**': [https://hack4u.io/wp-content/uploads/2022/05/portScan.sh](https://hack4u.io/wp-content/uploads/2022/05/portScan.sh)

Para comprobar cuáles son los puertos abiertos podríamos enviar una cadena vacía al puerto correspondiente y comprobar (con la && juntamos el comando mientras con los dos || es un OR)
~~~ bash
(echo '' > /dev/tcp/localhost/22) 2>/dev/null && echo "El puerto está abierto" || echo "El puerto está cerrado"
~~~

Más avanzado para la búsqueda
~~~ bash
for port in $(seq 1 65535); do
(echo '' > /dev/tcp/localhost/6600) 2>/dev/null && echo "[+] $port - OPEN" & 
done ; wait
~~~

Podríamos usarlo también para comprobar hosts activos en nuestra red:
~~~ bash
ping -c 1 192.168.1.5 &>/dev/null && echo "[+] El host está activo" || echo "[-] El host no está activo"
~~~

Para no tener que esperar si el host no está activo.
~~~ bash
timeout 1 bash -c "ping -c f 192.168.1.5 &>/dev/null" && echo "[+] El host está activo" || echo "[-] El host no está activo"
~~~

Podemos crear un directorio temporal (dentro de la carpeta /tmp) con *mktemp -d*

Archivo para listar todos los puertos para bandit 16-17:
~~~ bash
#!/bin/bash

function ctrl_c(){
        echo -e "\n\n[Saliendo...]\n"
        exit 1
}

#CTRL+C
trap ctrl_c INT

for port in $(seq 31000 32000); do
        (echo '' > /dev/tcp/127.0.0.1/$port) 2>/dev/null && echo "[+] $port - OPEN"
done
~
~~~

La forma más rápida sería con el comando *nmap*, el T5 es la plantilla de velocidad y es la más rápida (T1-T5), v es de verbose y -n es para que no aplique resolución DNS
~~~ bash
nmap --open -T5 -v -n -p31000-32000 127.0.0.1
~~~

Nos conectamos mediante Netcatç
~~~ bash
ncat --ssl localhost puertos_abiertos
~~~

Conseguiremos una clave por ssh, por lo que cambiamos sus permisos a lectura y escritura sólamente para el propietario (600), y usamos esta conexión para conectarnos mediante el localhost de la máquina
~~~ bash
ssh -i id_rsa bandit17@localhost -p 2220
~~~

Para comprobar todas las contraseñas del usuario actual símplemente cateamos */etc/bandit_pass/bandit17*

## Detección de diferencias entre archivos (b17-18)
Aquí dejo algunos ejemplos del comando *diff*:
- Comparar las diferencias con el comando diff: [https://eltallerdelbit.com/comando-diff-ejemplos/](https://eltallerdelbit.com/comando-diff-ejemplos/)

Podremos mirar primero cuantas líneas tiene cada archivo con el comando *wc -l*

Podremos usar el comando diff junto a los dos archivos para comprobar las diferencias:
~~~ bash
diff passwords.new passwords.old
~~~
Nos aparecerá esto:
- *<* -> Nos dirá que se ha quitado
- *>* -> Nos dirá que se ha cambiado por este

![diff](diff.png)
> En este caso la flag que se ha quitado es la correcta
{: .prompt-info} 

Si introducimos la Flag nos dirá que no tenemos acceso, porque si nos vamos a bandit18-19 en OverTheWire.org, nos dirá que se ha modificado la *.bashrc*.

## Ejecución de comandos por SSH (b18-19)
A través del archivo de configuración *.bashrc* o *.zshrc*, es posible definir una serie de acciones a llevar a cabo a la hora de obtener una consola interactiva, en este caso tras ingresar por SSH.

Es por ello que tras ingresar, somos expulsados de forma inmediata, dado que así ha sido definido en el archivo de configuración *.bashrc* para el caso que estamos tratando. Si colamos un comando al final de nuestra línea al aplicar una autenticación por SSH, lograremos que ese comando sea introducido a nivel de sistema antes de que se interprete el archivo de configuración pertinente.

Podríamos intentar inyectar un comando para traspasar el ssh, ejecutando el comando antes de que no permita conectarnos
~~~ bash
sshpass -p '810zq8IK64u5A9Lb2ibdTGBtlcSZsoe8' ssh bandit18@bandit.labs.overthewire.org -p 2220 bash
~~~

Este nos permitirá conectarnos a su shell (bash), por lo que si listamos nos aparecerá un archivo llamado *readme*, al cual mostrando su contenido nos aparecerá una flag que será para *bandit19*.

## Abusando de privilegio SUID para migrar de usuario (b19-20)
Los privilegios SUID ya vimos para qué servían, pero lo vuelvo a explicar.
**SUID** es un permiso de archivo especial para archivos ejectuables que permite a otros usuarios ejectuar el archivo con los permisos efectiovs del propieteario del archvivo. **SGID**, por el contrario, es un permiso de archivo especial que también se aplica a los archivos ejecutables y permite a otros usuarios heredar el GID efectivo del propietario del grupo de archivos.

**SUID** significa "*establecer ID de usuario*" (Set Owner User ID) y **SGID** significa "*establecer ID de grupo*" (Set Group ID up on Execution)

Nos permitirá ejecutar el archivo *bandit20-do*, el nos sirve para ejecutar archivos como bandit20 aunque seamos bandit19

Aquí podremos usar dos shells, las cuales se ejecutarán como bandit20
~~~ bash
./bandit20-do sh
./bandit20-do bash -p
~~~

Podremos ver la contraseña como dijimos anteriormente en */etc/bandit_pass/bandit20*

# Jugando con conexiones (b20-21)
Por aquí dejo un material de apoyo por si quereis indagar un poco más acerca del uso del comando **Netcat**:
- Usando Netcat - Algunos comandos prácticos: [https://blog.desdelinux.net/usando-netcat-algunos-comandos-practicos](https://blog.desdelinux.net/usando-netcat-algunos-comandos-practicos)

Si listamos nos aparecerá un SUID en el archivo *suconnect*, el cual si ejecutamos nos pedirá que introduzcamos un puerto.
> Realizará una conexión al localhost en el puerto que especifiquemos como un argumento de línea de comando. Este leerá una línea de texto desde la conexión y comparará esta a la contraseña del nivel anterior (bandit20). Si la contraseña es correcta, ésta transmitirá la contraseña al siguiente nivel (bandit21)
{: .prompt-info }

Podríamos abrir un puerto temporal como primera solución
~~~ bash
nc -nlvp 4646
~~~
- *n* -> No aplicará resolución DNS
- *l* -> Listen mode
- *v* -> Verbose
- *p* -> Puerto

Abrimos otra terminal y mientras una está escuchando por el puerto 4646, con la nueva ejecutaremos el script anteriormente visto *(suconnect)* a través del puerto 4646 también:
~~~ bash
./suconnect 4646
~~~

Nos aparecerá que tenemos conexión en nuestro localhost a través del puerto 60114:
> Connection received on 127.0.0.1 60114

Si intentamos otro comando nos aparecerá como Failed, pero como sabemos que tenemos que introducir la contraseña de nuestro usuario actual, símplemente la introducimos con la conexión establecida y nos aparecerá la nueva.
![conexioes.png](jugando-conconexiones.png)

## Abusando de tareas Cron (1/3) (b21-22)
Cron es un administrador de tareas de Linux que permite ejecutar comandos en un momento determinado, por ejemplo, cada minuto, día, semana o mes. Si queremos trabajar con cron, podemos hacerlo a través del comando *crontab*.

El formato de configuración de cron es el siguiente: *Minuto Hora Dia-del-mes Mes Dia-de-la-semana Comando-a-ejecutar*

El intervalo de tiempo se especifica mediante 5 campos que representan, de izquierda a derecha:
- **Minutos ->** de 0 a 59
- **Horas ->** de 0 a 23
- **Día del Mes ->** de 1 a 31
- **Mes ->** de 1 a 12
- **Día de la semana ->** de 1 a 6 lunes a sábado (1=lunes, 2=martes,etc) y o 7 el domingo.

**Ejemplo:**
*Minuto - Hora - Día del mes - Mes - Día de la semana*
> 30 9 20 7 4 -> A las 9:30 AM del día 20 de julio y que sea jueves
> 30 9 20 7 *  -> A las 9:30 AM del día 20 de julio sin importar el día de la semana.
> 20 * * * 6  -> Al minuto 20 de cada hora de los sábados
> 20 * * 1 6 -> Al minuto 20 de cada hora de los sábados de enero

Si quisiéramos especificar todos los valores posibles de un parámetro (minutos, horas, etc.) podemos hacer uso del asterisco (`*`).
Esto implica que si en lugar de un número utilizamos un asterisco, el comando indicado se ejecutará cada minuto, hora, día de mes, mes o día de la semana, como en el siguiente ejemplo:
`*****/home/user/script.sh`

En el directorio /etc/cron.d nos aparecerá las diferentes actividades para cron, por lo que si estamos bandit21, debemos ver el *cronjob_bandit22* que nos dirá:
`@reboot bandit22 /usr/bin/cronjob_bandit22.sh &>/dev/null`
`***** bandit 22 /usr/bin/cronjob_bandit22.sh &>/dev/null`
Por lo que nos dice que siempre está ejecutandose el archivo */usr/bin/cronjob_bandit22.sh*, por lo que si vemos que es:

![cron2](cronjobbandit22.png)
Osea, que cambia los permisos del fichero dentro de /tmp/ a 644 (rw-r--r--) y muestra la contraseña para bandit22 en este mismo archivo, por lo que si hacemos un cat a este nos aparecerá la flag para bandit22.

### Abusando de tareas cron (2/3) (b22-23)
¿Quieres ver algunos ejemplos para entender mejor la lectura e interpretación de las tareas Cron?, te dejo por aquí el siguiente material de apoyo:
- Cron & Crontab, explicados: [https://blog.desdelinux.net/cron-crontab-explicados/](https://blog.desdelinux.net/cron-crontab-explicados)
Al seguir casi los mismos pasos, (mirando el archivo */etc/cron.d/cronjob_bandit23*), nos dirá que el archivo que se ejecuta está en /usr/bin/cronjob_bandit23.sh, por lo que si miramos este script nos aparecerá lo siguiente:

![cron3](cronjobbandit23.png)

En mytarget está diciendo que somos el usuario bandit23 (ya que es para el cronjob de este mismo), le está haciendo un hash md5sum, cortando el delimitador ' ' (un espacio) y cogiendo el primer field (campo), apareciendo lo siguiente `8ca319486bfbbc3663ea0fbe81326349`, por lo que en */tmp/8ca319486bfbbc3663ea0fbe81326349* se está guardando la contraseña de bandit23.

### Abusando de tareas cron (3/3) (b23-24)
Con esta clase concluimos las tareas Cron.

Esta parte deciros que es fundamental, sobre todo de cara a los módulos de Hacking que vamos a estar tocando en la academia, pues en muchas de las ocasiones veremos cómo nos será necesario no sólo identificar qué tareas se ejecutan en el sistema a intervalos regulares de tiempo, sino también saber cómo poder abusar de estas para elevar nuestros privilegios.

Volvemos a ver los cronjobs que nos lleven a lo siguiente en bandit 24:
![cron4])cronjobbandit24.png)
Nos dice que cambia de directorio a */var/spool/bandit24/foo*, por lo que nos dirigiremos a /var/spool/bandit24 y listaremos, encontrando que la carpeta *foo* tiene los siguientes permisos:
`drwxrwx-wx 3 root bandit24 4096 Feb 12 22:51 foo`
Osea, que podremos escribir y acceder pero no leer (ya que somos *otros*), por lo que podremos crear cosas.

Con esto ya podemos saber que todo aquel script que se envíe podrá ser ejecutado como el usuario bandit24.

Creamos una ruta temporal, para ello probaremos a crear una variable del comando para crear una ruta temporal sin tener que introducir directamente (no es obligatorio pero así empezaremos a manejarnos mejor por bash scripting) y creamos un archivo:
~~~ bash
dir_name=$(mktemp -d)
cd $dir_name
vim script.sh
~~~

Lo haremos en este directorio temporal para no perderlo de vista, símplemente cateándolo después al directorio */var/spool/bandit24/foo*, y para tenerlo ya definido le pondremos permisos de ejecución para todos a este script `chmod +x script.sh`.
~~~ bash
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/tmp.WYeOVpyghT/bandit24_password.log
chmod o+r /tmp/tmp.WYeOVpyghT/bandit24_password.log
~~~
> Esto lo que consigue es hacer un cat sobre /etc/bandit_pass/bandit24 y enviar el output a nuestro directorio temporal.
{: .prompt-info }

Agregamos a otros permisos de escritura y acceso a nuestro directorio temporal:
`chmod o+wx $dir_name` o `chmod o+wx .`
Y copiamos nuestro script.sh al directorio objetivo:
`cp script.sh /var/spool/bandit24/foo/example`
`chmod +x /var/spool/bandit24/foo/example`

Podremos ver qué pasa en cada momento en el directorio actual con:
~~~ bash
watch -n 1 ls -l
~~~
Y podremos ver cómo al ejecutarse dentro del cronjob se incluirá un archivo que es el que hemos definido anteriormente como bandit24_password.log:

Si nos aparece como 0 bytes significa que no está bien diseñado el script, prueba con algunos cambios para que aparezca como la imagen de abajo, osea con 33bytes.
![bandit5](bandit24password_log.png)
![bandit6](bandit24passwgood.png)

## Comprendiendo las expresiones de las tareas Cron
Por aquí comparto un enlace para que lo tengáis a mano y podáis practicar más por vuestra cuenta:
- [https://www.site24x7.com/es/tools/crontab/cron-generator.html](https://www.site24x7.com/es/tools/crontab/cron-generator.html)