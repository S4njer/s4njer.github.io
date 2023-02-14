### Comandos básicos Linux

**whoami** = Permite ver el usuario que eres

**!$** = Volverá a escribir la última opción del comando:
	EJ: 
~~~ shell
cat /etc/passwd
ls -l !$
.rw-r--r-- root root 2.7 KB Fri Jan 20 09:32:20 2023  /etc/passwd
~~~

**id** = Permite ver a qué grupo pertenece tu usuario

**which** = Permite ver la ruta absoluta de cualquier archivo-fichero.
  *En caso de no existir el comando se puede usar:*
   - *command -v "archivo-fichero-comando"*

**pwd** = Nos mostrará la ruta absoluta hasta nuestro directorio actual.

**echo** = Mostará lo que hayamos querido (Ya sea ruta, alguna palabra para mostrar en pantalla)
  - *echo $PATH*: Muestra el camino absoluto a nuestro usuario
  - *echo $?*: Muestra si el comando anterior ha sido exitoso

**ls** = lista el directorio que queramos.

**cd** = Change Directory (Cambiar de directorio)
***

#### Rutas absolutas de archivos principales
/etc/group = Todos los grupos existentes en el equipo
/etc/passwd = Muestra el identificador de usuario, grupo al que pertenece.
/etc/shells = Muestra las shells existentes
/dev/null = Recurso existente es un archivo especial, cualquier cosa que metamos aquí desaparecerá.
***
#### Separadores y concatenadores
**;** = Separa comandos y ejecuta los comandos aunque uno dé algún tipo de fallo.
**&&** (AND) = Se ejecuta si el primer comando es exitoso.
**||** (OR) = Ejecutará todos los correctos

>command var-name | *xargs* ls -l : xargs permitirá concatenar un comando a parte del original, permitiéndonos hacer un listado -l.

***
#### Control del flujo stderr-stdout, operadores y procesos en segundo plano.
Bash Redirections Cheat Sheet: https://hack4u.io/wp-content/uploads/2022/05/bash-redirections-cheat-sheet.pdf

- **stdin ->** Buscará el comando scp pero no quieres que te devuelva el introducido:
	-  *cat .zsh_history | grep scp 1>&/dev/null*
- **stdout ->** El flujo de salida estándar es el destino predeterminado de la salida para las aplicaciones
	- *miaplicacion > ejemplo.txt*  (su id es 2)
		para redirigir la salida de myapplication al archivo ejemplo.txt en lugar de a la consola.
		
- **stderr ->** Devolverá el resultado pero todos los errores no:
	- *find / -name "archivo.txt 3>&/dev/null"*


- Ejemplos:
1. **wireshark &>/dev/null** = Este proceso eliminará el output que aparecerá al abrir el programa.
2. **wireshark &>/dev/null &** = El proceso se ejecutará en 2º Plano pero al cerrar la terminal también lo hará el programa.
3. **wireshark &>/dev/null & disown** = Desasociará el programa de la terminal, por lo que al cerrar la terminal el programa estará corriendo.
***
#### Descriptores de archivo.
*(Es más por cultura general, no es obligatorio)*

> Generalmente, un descriptor de archivo es una clave a una estructura de datos residente en el núcleo, que contiene detalles de todos los archivos abiertos.

**file** = Este comando nos permitirá ver qué tipo de airchivo es, lo que ocupa de tamaño y si está vacio o lleno. (En el comando de abajo no se refiere a este comando sino a un archivo)
~~~	
	"<"    = Lectura
	">"    = Escritura
	"<>"  = Lectura y escritura
~~~
- **exec 3<> file** = Estamos empleando en descriptor de archivo identificado con el número 3.

- **whoami >&3**= Envias el comando *whoami* a ese descriptor de archivo osea, el anterior creado, (lin. 51) por lo que si hacemos un *cat* a ese archivo podremos ver el resultado de este.

- **exec 3>&-**= Cierras el descriptor de archivos que tiene como identificador el número 3, por lo que no puedes almacenar cosas en él.

- **exec 8>&5** = Crea una copia del archivo 5 con el identificador 8, por lo que lo tendrás guardado todo en dos archivos.

*** 
#### Cuestionario de control de flujo y operadores. (75% Aprobado)

¿Qué estaré almacenando en el archivo 'file' con este comando?
~~~
whoam 2> file
~~~
- **El stderr**
> El valor 2 redirige los errores standard (stderr) a un archivo.
- **Commando not found: whoam**
> Este probablemente sería el mensaje de error que almacenaríamos en este archivo, siempre y cuando el comando 'whoam' no exista.

¿Cómo puedo cerrar el descriptor de archivo una vez hemos depositado el contenido deseado?
~~~
- exec 3<> file
- whoami >&3
- cat file
------------------
	| File: file
	| Size: 8 B
------------------
  1 | s4njer
------------------
~~~
- **exec 3>&-**
> De esta forma, estaremos cerrando el descriptor de archivo creado. Si en este punto, probáramos a enviar un output a este descriptor de archivo, nos saldría un error de tipo '3: bad file descriptor'.

¿Qué se estará almacenando en el archivo 'test' a la hora de aplicar este comando?
~~~
- whoami &> test
~~~
- **Se almacenará tanto el stderr como el stdout del comando aplicado**
> El control de flujo '&>' sirve para redirigir tanto el stderr como el stdout a un archivo. Otra forma de representar este mismo control de flujo sería con '> test 2>&1' 

¿Cómo puedo enviar el output del comando 'whoami' al descriptor del archivo creado?
~~~
- exec 3<> file
~~~
- **whoami >&3**
> A través de este comando, depositaremos el contenido del comando en el descriptor de archivo previamente creado.

¿Son estos dos controles de flujo lo mismo?
~~~
- whoam > file1 2>&1
- whoam &> file 2
- ls -l file*
  .rw-r--r-- s4njer s4njer 30B Sun May 22 01:05:18 2022 file1
  .rw-r--r-- s4njer s4njer 30B Sun May 22 01:05:24 2022 file2
~~~
- **Verdadero**
> Recuerda que el uso del operador '&>' sirve para indicar que tanto el stderr como el stdout va a ser redirigido a un archivo, igual que la instrucción que está previamente definida.

¿Qué sucederá cuando se aplique el último comando?
~~~ s
- exec 3<> file
- exec 4>&3-
- whoami >&4
- whoami >&3
~~~
- **El comando causará un error, dado que el descriptor de archivo 3 ha sido cerrado.**
> El comando 'exec 4>&3' lo que hace es crear un descriptor de archivo 4 que actúa como copia del descriptor 3 pero cerrando este una vez la copia es creada. Lo normal es ver un error de tipo '3: Bad file descriptor', dado que el descriptor ya no existe.

¿Cómo puedo redirigir el output de un comando a un archivo?
- **whoami > file**
> Este comando redirige el standard output (stdout) a un archivo.

- **(whoami &/dev/null) > file**
> Si no hubieran habido unos paréntesis, pese a no ver el output por consola, este habría sido almacenado en el archivo, pero al llevar el paréntesis lo que rediriges al archivo file es el output de ese comando ejecutado a nivel de sistema, que es un vacío, no teniendo por tanto ningún contenido el archivo 'file'. Esta respuesta es incorrecta.

- **whoami 1> file** 
> El valor 1 corresponde al descriptor de archivo por defecto para stdout, por lo tanto estaremos redirigiendo mismamente el output del comando.

¿Sabrías decir qué es lo que está pasando aquí?
~~~
- exec 3<> file
- exec 4>&3
- whoami >&4
- exec 4>&-
- exec 3>&-
~~~

- **Se comienza utilizando un descriptor de archivo con capacidad de lectura y escritura para posteriormente crear una copia del descriptor e archivo al número 4. Al enviar el output del comando, se almacenará en el mismo archivo 'file' y posteriormente se cierran ambos descriptores de archivo**
> La operación 'exec 4>&3' crea un descriptor de archivo 4 el cual actúa como copia del descriptor de archivo 3. En caso de haber querido cerrar el primer descriptor tras establecer la copia, podríamos haber hecho 'exec 4>&3-'