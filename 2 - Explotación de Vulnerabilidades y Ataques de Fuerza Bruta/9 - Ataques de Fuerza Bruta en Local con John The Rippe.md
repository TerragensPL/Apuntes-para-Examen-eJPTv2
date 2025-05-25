# Ataques de Fuerza Bruta en Local con John The Ripper

### C**rackear un archivo ZIP protegido con contraseña**

Vamos a crear un archivo comprimido ZIP en nuestro Windows  protegido por la contraseña **password1** y lo pasaremos al escritorio de nuestra Kali.

![image.png](./imagenes/image%20109.png)

En mi caso tengo activada la carpeta compartida y el portapapeles con mi maquina virtual Kali.

Pero en caso de no ser asir y que se este compartiendo el adaptador de red podemos hacerlo de la siguiente manera:

- Siendo la IP de mi maquina Windows 192.168.0.50 levantaremos un servidor web básico en nuestro Windows con ayuda de Python. Python debe estar instalado.
    
    Ejecutamos PowerShell
    
    ```bash
    python -m http.server 80
    ```
    
    ![image.png](./imagenes/image%20110.png)
    

- En nuestra Kali
    
    ```bash
    wget 192.168.0.50/archivos.zip
    ```
    
    ![image.png](./imagenes/image%20111.png)
    
- Imaginemos que dentro de algún servidor o maquina hay un archivo .ZIP protegido con contraseña. Lo interesante es crakear la contraseña y para ello utilizaremos **John The Ripper.**
- Para  crackear un zip debemos pasarle primero una herramienta que tiene **John The Ripper** que es **zip2john** para extraerle el **hash.**
    
    ```bash
    zip2john archivos.zip > hash
    ```
    
    ![image.png](./imagenes/image%20112.png)
    
    - **zip2john archivos.zip**: Procesa el archivo ZIP y extrae cualquier hash relacionado con la contraseña.
    - **> hash**: Guarda los resultados en un archivo llamado `hash`.

- Vamos a crackear el ZIP
    
    ```bash
    john --wordlist=/usr/share/wordlists/rockyou.txt hash
    ```
    
    ![image.png](./imagenes/image%20113.png)
    

1. john --wordlist=/usr/share/wordlists/rockyou.txt hash
**`john`**: Llama a la herramienta **John the Ripper**, diseñada para romper contraseñas utilizando diferentes métodos, como fuerza bruta o ataques basados en diccionarios.
2. **`-wordlist=/usr/share/wordlists/rockyou.txt`**: Especifica que se utilizará un archivo de diccionario, en este caso `rockyou.txt`, que contiene una lista precompilada de contraseñas comunes. Este archivo está ubicado en el directorio `/usr/share/wordlists`. Es particularmente famoso porque contiene millones de contraseñas reales filtradas en incidentes de seguridad anteriores.
3. **`hash`**: Representa el archivo que contiene los hashes de contraseñas que se quieren descifrar. Estos hashes deben haber sido generados previamente con una herramienta como `zip2john` (para ZIPs).

### Cr**ackear una base de datos** de del **gestor de contraseñas KeePass**

- Vamos a probar a **crackear una base de datos** de del **gestor de contraseñas KeePass**. podemos descargar la versión Portables desde aquí: [KeePass Password Safe](./imagenes/https://keepass.info/) en el siguiente apartado. Yo voy a utilizar una versión vieja la 2.48.1 ya que en el momento que escribo estos apuntes **John the Ripper** no soporta la seguridad de las nuevas versiones de KeePass.
    
    ![image.png](./imagenes/image%20114.png)
    
    De contraseña  le ponderemos también **password1 .** Compartimos de nuevo con nuestra maquina Kali con **extensión kdbx** y ponemos el archivo en el escritorio.
    
    ![image.png](./imagenes/image%20115.png)
    
    ```bash
    keepass2john Database.kdbx > hash
    ```
    
    ![image.png](./imagenes/image%20116.png)
    
- Craqueamos la contraseña.
    
    ```bash
    john --wordlist=/usr/share/wordlists/rockyou.txt hash
    ```
    
    ![image.png](./imagenes/image%20117.png)
    

### Cr**ackear un Hash**

Como prueba de concepto vamos a generar el hash de de la contraseña **password1**, esto lo haremos en la pagina web [https://www.md5hashgenerator.com/](./imagenes/https://www.md5hashgenerator.com/) 

![image.png](./imagenes/image%20118.png)

MD5: 7c6a180b36896a0a8c02787eeafb0e4c

SHA1: e38ad214943daad1d64c102faec29de4afe9da3d

Muchas veces nos encontraremos las contraseñas en hasheadas para protegerlas.

- Guardamos el Hash dentro de un archivo llamado por ejemplo hash.
    
    ```bash
    nano hash
    ```
    
    ![image.png](./imagenes/image%20119.png)
    
    Podemos ver su contenido 
    
    ```bash
    cat hash
    ```
    
    ![image.png](./imagenes/image%20120.png)
    

- Vamos a Crakearlo
    
    ```bash
    john --wordlist=/usr/share/wordlists/rockyou.txt hash
    ```
    
    ![image.png](./imagenes/image%20121.png)
    
    No nos a encontrado la contraseña 😲😲. Esto puede ser por dos motivos el primero que **la contraseña no se encuentre en el diccionario rockyou.txt** (pero en nuestro casa sabes que si esta). Y la segunda es que es importante que le digamos a John the Ripper que tipo de hash es el que estamos intentando crakear.
    
    Para identificar que tipo de hash es vamos a usar la herramienta que tenemos en Kali , **hash-identifier**.
    
    ![image.png](./imagenes/image%20122.png)
    
    Lo ejecutamos y le pegamos el hash que estamos intentando crackear.
    
    ![image.png](./imagenes/image%20123.png)
    
    Vemos que es un **hash MD5**. Para saber que poner como decírselo a **John the Ripper** vamos a mirar el intento fallido donde nos habrá mostrado las instrucciones.
    
    ![image.png](./imagenes/image%20124.png)
    

```bash
john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt hash
```

![image.png](./imagenes/image%20125.png)

**NOTA:** Como alternativa para crakear hash tenemos la **herrmienta hashcat**