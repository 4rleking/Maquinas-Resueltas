# MATRIX-BREAKOUT: 2 MORFEO

Para descargar la máquina ir a https://www.vulnhub.com/entry/matrix-breakout-2-morpheus,757/

Iniciamos las 2 máquinas, en este caso iniciamos **(MATRIX-BREAKOUT: 2 MORFEO)** y en su defecto nuestra máquina personal **(Kali)**, al iniciar las dos máquinas, realizamos un escaneo de nuestra red:

<img src=Matrix-morfeo-2\Imagenes\arp-scan.jpg align=center heigth="400px" width="950px">

Una vez identificado el host de la máquina objetivo, utilizamos la herramienta nmap:

<img src=Matrix-morfeo-2\Imagenes\nmap-sV-sC.jpg align=center heigth="400px" width="950px">

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