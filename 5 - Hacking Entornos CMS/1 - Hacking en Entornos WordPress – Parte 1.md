Maquinas a utilizar **Kali Linux y Internal** [https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)

  

> [!important] WordPress es uno de los CMS más conocidos y populares a día de hoy, por lo que resulta fundamental mantenerlo protegido y securizado, aunque disponemos de herramientas como WPscan que nos permite realizar una auditoría de este CMS y poder enumerar tanto plugins vulnerables como usuarios existentes

  

- Miramos si tenemos conectividad con la maquina victima.
    
    ```Bash
    ping -c 1 10.10.32.70
    ```
    
    ![[Imagenes/image 51.png|image 51.png]]
    
    Vemos que es una maquina Linux.
    

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.10.32.70
```

![[Imagenes/image 1 40.png|image 1 40.png]]

Como estamos ante un WordPress el puerto 80 esta abierto.

  

- Nos vamos al navegador web ponemos la IP y miramos la web de WordPress. Nos muestra una plantilla de Apache.
    
    ![[Imagenes/image 2 39.png|image 2 39.png]]
    

  

- Vamos a revisar el código fuente por si encontramos comentarios o alguna pista. **Pulsamos CTRL+U**.
    
    ![[Imagenes/image 3 38.png|image 3 38.png]]
    
    No vemos nada significativo.
    
      
    
- Vamos a hacer **Fuzzing Web** para descubrir los subdirectorios**.**
    
    ```Bash
    gobuster dir -u http://10.10.32.70/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 4 36.png|image 4 36.png]]
    

  

- Probamos en el navegador web estos dos directorios que hemos encontrado.
    
    ![[Imagenes/image 5 35.png|image 5 35.png]]
    
      
    
    ![[Imagenes/image 6 33.png|image 6 33.png]]
    

> [!important] Carga paginas muy parecidas pero debemos fijarnos en el detalle de que las **carga mal y contenido incompleto**. Esto **pasa por que hace falta que la IP de la maquina victima este apuntando al dominio de WordPress y muchisima información viene del dominio.**
> 
> Para ello debemos poner la URL en **/etc/hosts/**

- ¿Cómo averiguamos la URL? miramos en el código fuente la pagina .
    
    ![[Imagenes/image 7 31.png|image 7 31.png]]
    
    Vemos que se repite en muchísimas ocasiones llama a **http:/internal.thm,** esto significa que hay un dominio.
    
- Añadimos el dominio **http:/internal.thm** al archivo **/etc/hosts/.**
    
    ```Bash
    sudo nano /etc/hosts 
    ```
    
    ![[Imagenes/image 8 29.png|image 8 29.png]]
    

  

- Refrescamos la pagina web por ejemplo la del blog. Y vemos que ya carga bien.
    
    ![[Imagenes/image 9 26.png|image 9 26.png]]
    

  

  

> [!important] Cunado estamos en un WordPress hay una **ruta** que en la mayoría de las ocasiones suele estar **expuesta en paginas vulnerables**, se trata del **directorio wp-login**.

- En el navegador web escribimos lo siguiente `**10.10.32.70/blog/wp-login**`
    
    ![[Imagenes/image 10 22.png|image 10 22.png]]
    
    Si no cargar como nos a ocurrido escribimos `[http://10.10.32.70/blog/wp-login.php](http://10.10.32.70/blog/wp-login.php)` .
    
    ![[Imagenes/image 11 16.png|image 11 16.png]]
    
      
    
    Esto también podríamos haberla encontrado haciendo **Fuzzing Web.**
    
    ```Bash
    gobuster dir -u http://10.10.32.70/blog/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
    ```
    
    ![[Imagenes/image 12 16.png|image 12 16.png]]
    
      
    
- A partir de este punto debemos utilizar la herramienta **WPScan.** Supongamos que yo no se ni el usuario ni la contraseña. Con **WPScan** voy a **encontrar usuarios validos** que luego usare para hacer ataques de fuerza bruta.

  

> [!important] **Continua en Hacking en Entornos WordPress – Parte 2**