Maquinas a utilizar **Kali Linux y Metasplotable2**

  

Vamos a ver como podemos utilizar Wireshark para ver información sensible si se esta utilizando protocolos vulnerable como puede ser **http , ftp** .

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 42.png|image 42.png]]

  

  

- Abrimos **Wireshark** escogiendo la red en la que están conectadas las maquina en mi caso **eth0.**
    
    ![[Imagenes/image 1 31.png|image 1 31.png]]
    
    ![[Imagenes/image 2 30.png|image 2 30.png]]
    

En cuando abra un navegador o cualquier cosa que utilice el eth0 la pantalla de **Wireshark** se llenara de trafico de red.

![[Imagenes/image 3 29.png|image 3 29.png]]

  

- Aremos una prueba entrando en la maquina utilizando el protocolo ftp. Sabiendo que el **usuario y la contraseña** son **msfadmin**.
    
    ```Bash
    ftp 10.0.3.7
    ```
    
    ![[Imagenes/image 4 28.png|image 4 28.png]]
    
      
    
    Como hemos dicho antes esta comunicación por este protocolo no esta encriptada por tanto si hay un sniffer como es **Wireshark** las credenciales abran sido expuestas**.**
    

  

- Filtramos paquetes escribiendo **ftp** y pulsando Enter en la barra de Wireshark.
    
    ![[Imagenes/image 5 27.png|image 5 27.png]]
    
    ![[Imagenes/image 6 25.png|image 6 25.png]]
    

  

- Aremos otra prueba usando el protocolo http.
    - Abrimos el navegador web y introducimos la IP de la maquina victima y pulsamos en la opción DVWA.
        
        ![[Imagenes/image 7 23.png|image 7 23.png]]
        
        ![[Imagenes/image 8 21.png|image 8 21.png]]
        

- Escribimos como **usuario admin** y de **contraseña password**
- Nos vamos a Wireshark y aplicamos un **filtro http**.
    
    ![[Imagenes/image 9 20.png|image 9 20.png]]