Maquinas que vamos a usar Kali Linux y Unrecover.

Enlace para descargar Unrecover: [DockerLabs](https://dockerlabs.es/)

**NOTA: La maquina Unrecover es un docker y tenemos que desplegarlo antes de poder usarla.**

- Vamos a colocar los dos archivos de la maquina en el escritorio de nuestro Kali para tenerlos a mano.
    
    ![[Imagenes/image 25.png|image 25.png]]
    

  

- La desplegamos
    
    ```Bash
    sudo bash auto_deploy.sh unrecover.tar
    ```
    
    Quizás no tengamos instalado Docker en nuestro sistema como es mi caso y empezara a descargar e instalar los archivos necesarios.
    
    ![[Imagenes/image 1 14.png|image 1 14.png]]
    
      
    
- En otra terminal Le vamos ha hacer un escaneo de puertos con nmap.
    
    ```Bash
    sudo nmap -p- -sS -sC -sV --min-rate 5000 -n -Pn -vvv 172.17.0.2
    ```
    
    ![[Imagenes/image 2 13.png|image 2 13.png]]
    
    Vemos que esta **abierto el puerto 3306**, este es el **puerto** por el que corren **por defecto las bases de datos**. También por defecto **una base de datos no debería estar expuesta de esta forma por un puerto abierto**.
    
    **Si decides abrirlo**, asegúrate de **tomar medidas de seguridad** como:
    
    - **Configurar un firewall** para limitar el acceso solo a direcciones IP confiables.
    - **Usar contraseñas fuertes** y cifrado para proteger las conexiones.
    - **Deshabilitar usuarios innecesarios** y restringir privilegios.
    
    Si no necesitas acceso remoto, es mejor mantener el puerto cerrado para minimizar riesgos.
    

  

- Vamos ha hacerle un ataque de fuerza bruta a esta base de datos. Pero necesitamos un usuario.
    
    Vamos a investigar vemos que esta corriendo por el puerto 80 un servidor web apache.
    
    ![[Imagenes/image 3 12.png|image 3 12.png]]
    

  

- Abrimos el navegador web y introducimos la IP de la maquina victima para acceder al contenido del puerto 80.
    
    ![[Imagenes/image 4 11.png|image 4 11.png]]
    

Vemos que tenemos una pista hay un usuario que se llama **capybara.** Esto en la vida real no suele ocurrir pero esto es una demostración.

Utilizaremos el **diccionario rockyou.txt** , aunque ya lo tememos nos lo **descargamos al escritorio** para tenerlo a mano y así si un día no lo tenemos y lo necesitamos ya sabemos de donde descargarlo.

![[Imagenes/image 5 10.png|image 5 10.png]]

  

- Usamos Hydra
    
    ```Bash
    hydra -l capybara -P rockyou.txt mysql://172.17.0.2
    ```
    
    ![[Imagenes/image 6 9.png|image 6 9.png]]
    
      
    
- Accedemos a la base de datos con las credenciales que hemos obtenido.
    
    ```Bash
    mysql -u capybara -ppassword1 -h 172.17.0.2
    ```
    
    ![[Imagenes/image 7 8.png|image 7 8.png]]
    
      
    
    Nos genera un error. Este error ocurre porque el cliente MySQL está intentando establecer una conexión segura mediante TLS/SSL, pero el servidor no tiene soporte para SSL habilitado. Aquí hay algunas posibles soluciones:
    
    - Vamos a intentar solucionarlo:
        
        Lanzamos un contenedor de Docker para lanzar un instancia de una maquina distinta a mi maquina kali (en este caso de Debian)y no haiga ninguna incompatibilidad.
        
        ```Bash
        sudo docker run -it debian:latest
        ```
        
        ![[Imagenes/image 8 7.png|image 8 7.png]]
        
          
        
    - Actualizamos todos los repositorios.
        
        ```Bash
        apt update
        ```
        
        ![[Imagenes/image 9 7.png|image 9 7.png]]
        
    
      
    
    - Instalamos un cliente de base de datos Mariadb
        
        ```Bash
        apt install mariadb-client
        ```
        
        ![[Imagenes/image 10 7.png|image 10 7.png]]
        

- Ya tenemos nuestro contenedor listo y desde el nos conectamos a la base de datos.
    
    ```Bash
    mysql -u capybara -ppassword1 -h 172.17.0.2
    ```
    
    ![[Imagenes/image 11 6.png|image 11 6.png]]
    
      
    
    Nos movemos un poco por la Base de datos:
    
    `show databases;`
    
    `use beta;`
    
    ![[Imagenes/image 12 6.png|image 12 6.png]]
    
      
    
    `show tables;`
    
    `SELECT * FROM registraton;`
    
    ![[Imagenes/image 13 6.png|image 13 6.png]]
    
    Nos encontramos con un **nombre de usuario** y su **contraseña hasheada (**por lo general **las contraseñas en una base de datos se guarda hasheada).**
    
- Utilizaremos la Web [https://crackstation.net/](https://crackstation.net/) para romper el hash. ya que es una contraseña débil.
    
    ![[Imagenes/image 14 4.png|image 14 4.png]]