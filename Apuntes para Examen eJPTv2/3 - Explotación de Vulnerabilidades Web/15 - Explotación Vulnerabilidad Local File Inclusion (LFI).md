Maquinas a utilizar **Kali Linux y Friendly2** [https://hackmyvm.eu/machines/machine.php?vm=Friendly2](https://hackmyvm.eu/machines/machine.php?vm=Friendly2)

  

Intentaremos explotar la de forma básica la **vulnerabilidad local file inclusion**, la cual se trata de una vulnerabilidad que nos permite acceder a archivos internos de la máquina víctima; y así obtener información privilegiada que nos sirva para realizar otros ataques.  
  
En esta ocasión, vamos a tratar de acceder al archivo id_rsa del usuario existente en el sistema para conseguir entrar vía ssh sin proporcionar ninguna contraseña.  

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 41.png|image 41.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.16 -oN escaneo
```

![[Imagenes/image 1 30.png|image 1 30.png]]

  

- Nos vamos al navegador web y copiamos la IP de la maquina victima.
    
    ![[Imagenes/image 2 29.png|image 2 29.png]]
    

  

- Hacemos **Fuzzing Web,** y voy a utilizar **dirb** para hacerlo de una manera rápida.
    
    ```Bash
    dirb http://10.0.3.16/ 
    ```
    
    ![[Imagenes/image 3 28.png|image 3 28.png]]
    

  

- Miramos el directorio tools en el navegador.
    
    ![[Imagenes/image 4 27.png|image 4 27.png]]
    

  

- Pulsamos `CTRL+U` para revisar el código de la pagina web.
    
    ![[Imagenes/image 5 26.png|image 5 26.png]]
    
    Nos fijamos en un comentario que hace referencia a una URL. `**check_if_exist.php?doc=keyboard.html**`
    
    Vamos a probarla:
    
    ![[Imagenes/image 6 24.png|image 6 24.png]]
    

  

> [!important] Una vulnerabilidad de Local File Inclusion (LFI) en una página web se produce cuando una aplicación permite a un atacante incluir archivos locales del servidor para ejecutarlos o leer su contenido. Esto sucede debido a una validación deficiente o inexistente en los parámetros que controla el usuario.

  

Debemos empezar a pensar en esta posible vulnerabilidad cuando dentro de la URL estemos viendo algo parecido a esto `?doc=keyboard.html` donde la **URL esta llamando a un archivo**. Esta vulnerabilidad consiste en que yo pueda retroceder por los directorios de tal forma de que yo pueda estar apuntando hacia un archivo interno de la maquina victima. **Esto podría ser muy grabe** ya que podría **estar apuntando sobre archivos que contengan información sensible.**

  

- ¿Cómo puedo retroceder por los directorios?
    - Teniendo esta URL :
        
        `http://10.0.3.16/tools/check_if_exist.php?doc=keyboard.html`
        
    - Borramos el `keyboard.html` y lo sustituimos por `../` (esto viene de que en Linux para retroceder en los directorios ponemos `cd ..` ) por tanto vamos a poner muchos ../ sin limite ya que lo que queremos es llegar a la raíz y es mejor poner de mas que quedarnos cortos. Cuando se suele probar esto se suele intentar apuntar hacia un archivo llamado **passwd** que es un archivo donde se encuentran los usuarios registrados de la maquina Linux.
        
        `http://10.0.3.16/tools/check_if_exist.php?doc=../../../../../../etc/passwd`
        
        ![[Imagenes/image 7 22.png|image 7 22.png]]
        
        Pero cunado se intenta hacer un **File Inclusion** lo interesante es entrar dentro de la maquina. ¿Cómo podríamos convertir esto en una intrusión en el servidor?.
        
        Vamos a hacerlo de una manera simple, intentando apuntar contra el fichero **id_rsa de un usuario existente.** Si consigo obtenerlo dentro de mi maquina local podría entrar vía SSH sin introducir contraseñas.
        
        > [!important] Un archivo **id_rsa** es la clave privada de un par de claves RSA que se utiliza para la autenticación segura en conexiones SSH.<br><br>
        
          
        
        ![[Imagenes/image 8 20.png|image 8 20.png]]
        
        Podemos ver que tenemos un usuario antecedido de **home** es que el usuario existe. **/home/**==**gh0st**== .
        
        El fichero **id_rsa** se encuentra por defecto dentro de **/home/**==**nombre_ususario**==**/.ssh/**
        
        Por tanto la ruta que deberíamos poner nos quedaría así: **10.0.3.16/tools/check_if_exist.php?doc=../../../../../../home/gh0st/.ssh/id_rsa**
        
        ![[Imagenes/image 9 19.png|image 9 19.png]]
        
          
        
- Pulsamos **CTRL+U** para verlo mejor
    
    ![[Imagenes/image 10 18.png|image 10 18.png]]
    
      
    
- Nos copiamos el contenido.
- abrimos un terminal y creamos un fichero llamado id_rsa con nano
    
    ```Bash
    nano id_rsa
    ```
    
    ![[Imagenes/image 11 14.png|image 11 14.png]]
    
    ![[Imagenes/image 12 14.png|image 12 14.png]]
    

  

- Establecemos los permisos del archivo id_rsa para que solo el propietario pueda leer y escribir en él.
    
    ```Bash
    chmod 600 id_rsa
    ```
    
    ![[Imagenes/image 13 14.png|image 13 14.png]]
    
      
    
- Vamos loguearnos por ssh
    
    ```Bash
    ssh -i id_rsa gh0st@10.0.3.16
    ```
    
    ![[Imagenes/image 14 12.png|image 14 12.png]]
    
    `**i id_rsa**`: Especifica el archivo de clave privada (`id_rsa`) que se utilizará para la autenticación.
    
    `**gh0st**`: Es el nombre de usuario en el servidor remoto.
    
    `**10.0.3.16**`: Es la dirección IP del servidor remoto.
    
      
    
    **Nos pide una contraseña 😲😲**
    
    > [!important] **"Enter passphrase for key 'id_rsa'"** indica que la clave privada `id_rsa` está protegida con una frase de contraseña. Esta frase de contraseña es una capa adicional de seguridad que cifra la clave privada en tu sistema. Para usar la clave, debes ingresar la frase de contraseña que configuraste al generarla.
    > 
    > Esto asegura que, incluso si alguien obtiene acceso al archivo `id_rsa`, no podrá usarlo sin conocer la frase de contraseña.
    

  

- Vamos a **Crakear** la contraseña de **id_rsa** usando **John the Ripper** y su utilidad **ssh2john.**
    
    ```Bash
    ssh2john id_rsa > hash 
    ```
    
    ![[Imagenes/image 15 11.png|image 15 11.png]]
    
      
    
    ```Bash
    john --wordlist=/usr/share/wordlists/rockyou.txt hash 
    ```
    
    ![[Imagenes/image 16 8.png|image 16 8.png]]
    

  

- Volvemos a loguearnos por ssh
    
    ```Bash
    ssh -i id_rsa gh0st@10.0.3.16
    ```
    
    ![[Imagenes/image 17 6.png|image 17 6.png]]