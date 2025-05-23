![[Imagenes/image 7.png|image 7.png]]

**Escaneos de puertos**

Partiendo de un reconocimiento de equipos que componen una red por ejemplo con arp-scan (**NOTA:** es aconsejable utilizar varias herramientas para hacer un reconocimiento de la red para evitar que algún equipo de la red se quede sin detectar). Escogemos un equipo objetivo. En nuestro caso el equipo señalado en la captura que como vemos es una maquina virtual.

```Bash
sudo arp-scan -I eth0 --localnet 
```

![[Imagenes/image 1 5.png|image 1 5.png]]

  

Hacemos un Ping al equipo victima con una sola traza para intentar averiguar que clase de equipo es.

```Bash
ping -c 1 192.168.1.41 
```

![[Imagenes/image 2 4.png|image 2 4.png]]

  

Como vemos nos devuelve un **ttl de 128**. Esto nos da un gran porcentaje de seguridad que es una maquina **Windows.**

  

Queremos ver información mas concreta sobre la maquina , que puertos tiene abiertos, que corre en ellos y su versión, etec… . Para ello utilizaremos **nmap**.

- En su uso mas **básico de nmap** seria de esta manera:
    
    ```Bash
    nmap 192.168.1.41   
    ```
    
    ![[Imagenes/image 3 3.png|image 3 3.png]]
    
    Como se ve no muestra mucha información excepto los puertos abiertos.
    
      
    
- Vamos a hacer que **nmap** nos muestre los puertos abiertos y los que este corriendo tras ellos.
    
    ```Shell
    nmap -p- --open -sS -sV --min-rate 1500 -n -vvv -Pn 192.168.1.41 -oN escaneo.txt
    ```
    
    ![[Imagenes/image 4 3.png|image 4 3.png]]
    
    ### Explicación de los parámetros:
    
    > [!important] **-p-**: Indica que se deben **escanear todos los puertos, desde el 1 hasta el 65535**. De manera predeterminada, Nmap solo analiza los 1000 puertos más comunes, pero con este parámetro amplías el alcance.
    > 
    > **- -open**: Solo **muestra los puertos** que están **abiertos**, omitiendo los que estén cerrados o filtrados.
    > 
    > **-sS**: Realiza un **escaneo SYN** (half-open), el cual es rápido y menos detectable por sistemas de seguridad, ya que no establece conexiones completas.
    > 
    > **-sV**: Permite **identificar las versiones de los servicios que se ejecutan en los puertos** abiertos, brindando más detalles sobre el software en uso.
    > 
    > **- -min-rate 1500**: **Asegura una velocidad mínima de 1500 paquetes** enviados **por segundo**, acelerando el escaneo.
    > 
    > **-n**: **Desactiva la resolución de nombres DNS**, lo que hace que el análisis sea más rápido al no buscar los nombres de host asociados a las direcciones IP.
    > 
    > **-vvv**: Incrementa la verbosidad del comando, **mostrando más información en tiempo real** sobre el progreso del escaneo.
    > 
    > **-Pn**: **Prescinde del ping previo** para verificar si el host está activo, asumiendo que el objetivo está accesible aunque no responda a solicitudes ICMP.
    > 
    > **192.168.1.41**: Es la **dirección IP del objetivo** que se desea analizar. En este caso, es un dispositivo específico dentro de la red local.
    > 
    > **-oN escaneo.txt**: **Guarda los resultados** del escaneo **en un archivo** llamado escaneo.txt en un formato fácil de leer.
    
    ![[Imagenes/image 5 3.png|image 5 3.png]]