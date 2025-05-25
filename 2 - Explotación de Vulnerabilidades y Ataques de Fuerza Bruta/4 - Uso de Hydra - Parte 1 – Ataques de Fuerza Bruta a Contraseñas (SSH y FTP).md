# Uso de Hydra - Parte 1 – Ataques de Fuerza Bruta a Contraseñas (SSH y FTP)

- Maquinas que vamos a usas **Kali Linux** y **Friendly3**.
    
    Hacemos un escaneo de la red.
    
    ```bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ![image.png](./imagenes/image%2053.png)
    

Nuestra maquina victima es la **10.0.3.8** podemos ver que tipo de maquina es haciendo un Ping.

```bash
ping -c 1 10.0.3.8
```

![image.png](./imagenes/image%2054.png)

Vemos que es una maquina Linux

- Vamos a hacer un nmap de los puertos abiertos.
    
    ```bash
    nmap -p- --open -sS -sC -sV -min-rate 2000 -n -vvv -Pn 10.0.3.8 -oN escaneo
    ```
    
    ![image.png](./imagenes/image%2055.png)
    

Para hacer un **ataque de fuerza bruta debo conocer** o bien el **usuario o la contraseña**.

Para hacer estos ataques usaremos la herramienta Hydra.

- Vemos que esta abierto el puerto 80, así que vamos a probar a introducir en nuestro navegador web la IP de la maquina victima
    
    ![image.png](./imagenes/image%2056.png)
    
    Nos a cargado una simple pagina web donde se dirige a un **usuario** llamado **juan.**
    
- Vamos a atacar el **puerto 22 del SSH**
    
    **NOTA:** Mirar la sección Notas el punto numero 2 para los diccionarios.
    

```bash
hydra -l juan -P /usr/share/wordlists/rockyou.txt ssh://10.0.3.8
```

![image.png](./imagenes/image%2057.png)

Nos a encontrado una posible coincidencia **usuario: juan contraseña: alexis**

**Desglose de los parámetros del comando:**

1. **`hydra`**: Este es el comando principal que ejecuta la herramienta Hydra.
2. **`l juan`**: Este parámetro especifica el nombre de usuario fijo que se intentará usar durante el ataque. En este caso, el usuario es `juan`.
3. **`P /usr/share/wordlists/rockyou.txt`**:
    - `P` indica que se usará un archivo que contiene una lista de posibles contraseñas, conocida como *wordlist*.
    - `/usr/share/wordlists/rockyou.txt` es la ruta del archivo *rockyou.txt*, una lista muy conocida de contraseñas comunes que suele utilizarse para ataques de fuerza bruta.
4. **NOTA:**
    - **Minúscula (`l` y `p`)**: Se usan para especificar un único valor (usuario o contraseña fija).
    - **Mayúscula (`L` y `P`)**: Se usan para trabajar con listas (de usuarios o contraseñas).
    
5. **`ssh://10.0.3.8`**:
    - Aquí se especifica el protocolo y el objetivo del ataque.
    - `ssh://` indica que Hydra intentará conectarse a un servicio SSH (normalmente en el puerto 22 por defecto).
    - `10.0.3.8` es la dirección IP del objetivo donde se encuentra el servicio SSH.

**En resumen**, este comando intenta autenticar al usuario `juan` en el servidor SSH ubicado en `10.0.3.8`, probando todas las contraseñas de la lista contenida en el archivo `rockyou.txt`.

- Vamos a probar que funcionan las credenciales que nos a sacado.
    
    ```bash
    ssh [juan@10.0.3.8](./imagenes/mailto:juan@10.0.3.8) 
    ```
    
    ![image.png](./imagenes/image%2058.png)
    

- Vamos a atacar el **puerto 21 del FTP**
    
    Vamos pensar que el usuario es el mismo.
    
    ```bash
    hydra -l juan -P /usr/share/wordlists/rockyou.txt [ftp://10.0.3.8](./imagenes/ftp://10.0.3.8/)
    ```
    
    ![image.png](./imagenes/image%2059.png)
    

- Probamos las credenciales
    
    ```bash
    ftp 10.0.3.8
    ```
    
    ![image.png](./imagenes/image%2060.png)
    

Como nota decir que siempre es mejor entrar como SSH ya que se obtiene mas acceso a la maquina.