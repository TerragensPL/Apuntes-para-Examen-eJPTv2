# Uso de Wireshark para Interceptar Credenciales de Protocolos Vulnerables

Maquinas a utilizar **Kali Linux y Metasplotable2**

Vamos a ver como podemos utilizar Wireshark para ver información sensible si se esta utilizando protocolos vulnerable como puede ser **http , ftp** .

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%20204.png)

- Abrimos **Wireshark** escogiendo la red en la que están conectadas las maquina en mi caso **eth0.**
    
    ![image.png](./imagenes/image%20205.png)
    
    ![image.png](./imagenes/image%20206.png)
    

En cuando abra un navegador o cualquier cosa que utilice el eth0 la pantalla de **Wireshark** se llenara de trafico de red.

![image.png](./imagenes/image%20207.png)

- Aremos una prueba entrando en la maquina utilizando el protocolo ftp. Sabiendo que el **usuario y la contraseña** son **msfadmin**.
    
    ```bash
    ftp 10.0.3.7
    ```
    
    ![image.png](./imagenes/image%20208.png)
    
    Como hemos dicho antes esta comunicación por este protocolo no esta encriptada por tanto si hay un sniffer como es **Wireshark** las credenciales abran sido expuestas**.**
    

- Filtramos paquetes escribiendo **ftp** y pulsando Enter en la barra de Wireshark.
    
    ![image.png](./imagenes/image%20209.png)
    
    ![image.png](./imagenes/image%20210.png)
    

- Aremos otra prueba usando el protocolo http.
    - Abrimos el navegador web y introducimos la IP de la maquina victima y pulsamos en la opción DVWA.
        
        ![image.png](./imagenes/image%20211.png)
        
        ![image.png](./imagenes/image%20212.png)
        

- Escribimos como **usuario admin** y de **contraseña password**
- Nos vamos a Wireshark y aplicamos un **filtro http**.
    
    ![image.png](./imagenes/image%20213.png)