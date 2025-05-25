Aquí la sintaxis de Hydra cambia bastante de la que empleamos contra SSH y FTP.

- Maquinas que vamos a usas **Kali Linux** y **Blog**.

  

```Bash
sudo arp-scan -I eth0 --localnet  
```

![[Imagenes/image 23.png|image 23.png]]

  

Abrimos el navegador de internet y escribimos la siguiente ruta para acceder al panel de Login de esta maquina. Teniendo en cuenta siempre que la IP será la de nuestra maquina victima.

`10.0.3.9/my_weblog/admin.php`

![[Imagenes/image 1 12.png|image 1 12.png]]

  

Sabiendo que el **usuario es admin**. (algo esperable. Si alguna vez no sabemos el usuario siempre debemos probar admin por que en la mayoría de veces será un nombre de usuario valido)

- Para utilizar en este caso Hydra debemos indicarle la ruta exacta para llegar a este panel de Login. Para ello vamos a utilizar la **Herramienta BurpSuite ,** vamos a interceptar una petición http que nos mostrara muchos detalles entre ellos la ruta exacta del panel de Login.
    - Ejecutamos BurpSuite y le damos siguiente hasta la esta pantalla
        
        ![[Imagenes/image 2 11.png|image 2 11.png]]
        

- En el navegador nos vamos a la parte de ajustes
    
    ![[Imagenes/image 3 10.png|image 3 10.png]]
    
      
    
    **General→ Network Settings → Settings**
    
    ![[Imagenes/image 4 9.png|image 4 9.png]]
    
      
    
    En **manual proxy configuración** escribimos los siguientes datos:
    
    **HTTP Proxy : 127.0.0.1**
    
    **Port: 8080**
    
    ![[Imagenes/image 5 8.png|image 5 8.png]]
    

- Volvemos BurpSuite a la **pestaña Proxy** y pulsamos en **Intercept on.** Esto hará que BurpSuite se ponga entre el navegador y el servidor web esperando cualquier petición para poder interceptarla.
    
    ![[Imagenes/image 6 7.png|image 6 7.png]]
    

  

- En el Panel de Login ponemos **usuario login** y como **contraseña cualquier cosa** que se nos ocurra y le damos al botón login.
    
    ![[Imagenes/image 7 6.png|image 7 6.png]]
    

- En BurpSuite vemos que nos a interceptado la petición de validación.
    
    Como vemos nos **muestra** entre otras cosas el **usuario y el password** que he introducido a demás de la **ruta exacta del Panel Login** que ha sido **enviada empleando un POST**.
    
    **NOTA:** Un **POST** en una petición web es uno de los métodos del protocolo HTTP que se utiliza para enviar datos desde un cliente (como un navegador web) a un servidor.
    
      
    
    ![[Imagenes/image 8 5.png|image 8 5.png]]
    

  

- Desactivamos la interceptación de BurpSuite.
    
    ![[Imagenes/image 9 5.png|image 9 5.png]]
    
- Vamos a un terminal.
    
    ```Bash
    hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.0.3.9 http-post-form /my_weblog/admin.php:"username=admin&password=^PASS^:Incorrect username or password."
    ```
    

![[Imagenes/image 10 5.png|image 10 5.png]]

### **Desglose de los parámetros:**

1. `**hydra**`
    
    Hydra es una herramienta de fuerza bruta que se utiliza para encontrar credenciales (usuarios y contraseñas) en diferentes servicios como HTTP, FTP, SSH, etc.
    
2. `**l admin**`
    - `l` especifica un único nombre de usuario que Hydra usará en el ataque.
    - En este caso, el nombre de usuario definido es "admin".
3. `**P /usr/share/wordlists/rockyou.txt**`
    - `P` indica un archivo que contiene una lista de posibles contraseñas, también conocido como diccionario.
    - Aquí se utiliza el archivo `rockyou.txt`, que es un famoso diccionario de contraseñas comúnmente usado en pruebas de seguridad.
4. `**10.0.3.9**`
    - Especifica la dirección IP del servidor objetivo.
    - En este caso, el ataque está dirigido al servidor con IP `10.0.3.9`.
5. `**http-post-form**`
    - Indica que el ataque se realizará en un formulario web que utiliza el método HTTP POST.
    - Este tipo de ataque se dirige a aplicaciones web que tienen un inicio de sesión mediante formularios.
6. `**/my_weblog/admin.php:"username=admin&password=^PASS^:Incorrect username or password."**`
    
    - Especifica detalles sobre el formulario web y el patrón del ataque:
        - `/my_weblog/admin.php`: Ruta del formulario de inicio de sesión en el servidor.
        - `"username=admin&password=^PASS^`: Este es el cuerpo de la solicitud POST. Hydra reemplazará `^PASS^` con cada contraseña de la lista `rockyou.txt`.
        - `:Incorrect username or password."`: Mensaje de error que devuelve el servidor cuando las credenciales son incorrectas. Hydra usa este mensaje para identificar intentos fallidos.
    
      
    
    ![[Imagenes/image 11 4.png|image 11 4.png]]
    

- Vamos a probar las credenciales.
    
    Quitamos los cambios que hemos hecho en los ajustes del navegador.
    
    ![[Imagenes/image 12 4.png|image 12 4.png]]
    
      
    
    Introducimos las credenciales y ya tenemos acceso.
    
    ![[Imagenes/image 13 4.png|image 13 4.png]]
    
    **Nota:** Como vemos hemos dejado rastro de los intentos de Login erróneos en las notificaciones de Dashboard.