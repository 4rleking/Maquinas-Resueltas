# MATRIX-BREAKOUT: 2 MORFEO

Para descargar la máquina ir a https://www.vulnhub.com/entry/matrix-breakout-2-morpheus,757/

Iniciamos las 2 máquinas, en este caso iniciamos **(MATRIX-BREAKOUT: 2 MORFEO)** y en su defecto nuestra máquina personal **(Kali)**, al iniciar las dos máquinas, realizamos un escaneo de nuestra red:

```
┌──(root㉿kali)-[/home/al3j4ndr0]
└─# arp-scan -l
Interface: eth0, type: EN10MB, MAC: xx:xx:xx:xx:xx:xx, IPv4: 192.168.231.164
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.231.1   00:50:56:c0:00:08       (Unknown)
192.168.231.2   00:50:56:ea:8a:e1       (Unknown)
192.168.231.130 00:0c:29:8b:ff:c4       (Unknown)
192.168.231.254 00:50:56:e7:14:19       (Unknown)

4 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.304 seconds (111.11 hosts/sec). 4 responded
```

Una vez identificado el host de la máquina objetivo, utilizamos la herramienta nmap:

```
┌──(root㉿kali)-[/home/al3j4ndr0]
└─# nmap -sV -sC 192.168.231.130
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-11 20:10 CST
Nmap scan report for 192.168.231.130
Host is up (0.0031s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|_  256 aa83c351786170e5b7469f07c4ba31e4 (ECDSA)
80/tcp open  http    Apache httpd 2.4.51 ((Debian))
|_http-title: Morpheus:1
|_http-server-header: Apache/2.4.51 (Debian)
81/tcp open  http    nginx 1.18.0
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=Meeting Place
|_http-title: 401 Authorization Required
|_http-server-header: nginx/1.18.0
MAC Address: 00:0C:29:8B:FF:C4 (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.30 seconds
```

Podemos abrir el navegador e insertamos la dirección IP a ver que nos aparece.

<img src=Matrix-morfeo-2\Imagenes\Navegador.png>

Aplicamos detección de directorios con gobuster

```
┌──(root㉿kali)-[/home/al3j4ndr0]
└─# gobuster dir -u http://192.168.231.130 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 100 -b 404
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.231.130
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              txt,php,html
[+] Timeout:                 10s
===============================================================
2023/05/11 20:39:26 Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 280]
/.php                 (Status: 403) [Size: 280]
/index.html           (Status: 200) [Size: 348]
/javascript           (Status: 301) [Size: 323] [--> http://192.168.231.130/javascript/]
/robots.txt           (Status: 200) [Size: 47]
/graffiti.txt         (Status: 200) [Size: 139]
/graffiti.php         (Status: 200) [Size: 451]
/.html                (Status: 403) [Size: 280]
/.php                 (Status: 403) [Size: 280]
/server-status        (Status: 403) [Size: 280]
Progress: 882032 / 882244 (99.98%)
===============================================================
2023/05/11 20:42:01 Finished
===============================================================
```
Abrimos en el explorador la página "http://192.168.231.130/graffiti.php" y nos damos cuenta de que es un blog, el cual cualquier persona que tenga acceso puede dejar alg+un mensaje escrito para los demás que ingresen.

Dado esto, abrimos la herramienta de BurpSuite, y activamos el proxy, tanto en el Explorador como en BurpSuite; (En el explorador ingresamos la IP "127.0.0.1" y el puerto "8080"); dado esto nos vamos al explorador y enviamos un mensaje cualquier dentro del "graffiti.php", al hacer esto se envía la petición a BurpSuite.

Ya teniendo la petición en BurpSuite, seleccionamos con el segundo botón del mouse y seleccionamos la opcion de enviarlo al "Repeater", ahí podemos cambiar los valores que necesitamos.

Una vez realizado esto, intentamos cambiar el nombre del archivo, para saber si podemos generar archivos dentro del directorio del host.

Cuando enviamos la petición desde BurpSuite, nos vamos al explorador e intentamos ingresar al archivo que creamos desde BurpSuite y si nos da acceso al archivo, podremos crear los archivos que necesitemos.

Dado esto, buscamos una (Shell reverse) para PHP, "Yo tengo 1 código en mi repositorio funcional" que fue el que utilice para esta ocasión, copiamos el código y lo ingresamos en BurpSuite, en la sección "message=" y al final en la sección "file=" agregamos el nombre (shell.php). Una vez hecho esto, sólo enviamos la petición. Nota importante: tendremos que cambiar una IP en el código, que será nuestra IP local y el puerto que en esta ocasión seleccione el 4040 (pero puede ser cualquiera).

Nos dirigimos al explorador y desactivamos el proxy.

Nos dirigimos a la terminal e insertamos el siguiente código. (nc -lnvp 4040); con esto abrimos el puerto 4040 y lo dejamos esperando cualquier petición.

activado esto, nos dirigimos al explorador e intentamos abrir la página "http://192.168.231.130/shell.php", cuando intentamos abrir esta página, nos dirigimos a la terminal y vemos que la shell si funcionó y que ya tenemos acceso a la máquina.