Vamos a ver como poder encontrar equipos que estén dentro de mi red privada.

Para utilizar estas herramientas tendremos que tener en cuenta algunos datos que obtendremos utilizando el comando **ifconfig** en nuestra consola de Kali.

![[Imagenes/image 6.png|image 6.png]]

  

- **eth0** : Sera **nuestra interfaz** de red en la que nos encontramos.
- **192.168.1.37** : Es **nuestra dirección IP** que además nos señala nuestro segmento de red **192.169.1.0/24**

  

- **Herramienta ARP-SCAN:**
    
    ```Bash
    sudo arp-scan -I eth0 --localnet 
    ```
    
    El comando , sudo arp-scan -I eth0 --localnet, se utiliza para realizar un escaneo de red local y detectar los dispositivos conectados. Vamos a desglosarlo:  
      
    **Propósito del comando**  
    Este comando escanea la red local para identificar las direcciones IP y direcciones MAC de los dispositivos conectados en esa red.  
      
      
    **Explicación de los parámetros**  
      
    **sudo**: Otorga **permisos administrativos** necesarios para realizar el escaneo, ya que interactuar con la red requiere privilegios elevados.
    
      
    **arp-scan**: Es la **herramienta** que realiza el escaneo utilizando el protocolo ARP (Address Resolution Protocol). Esta herramienta envía paquetes ARP a la red para descubrir dispositivos.  
      
    **-I eth0**: Especifica **la interfaz de red a usar** para el escaneo. En este caso, eth0 indica una interfaz Ethernet. Si necesitas escanear en otra interfaz (como Wi-Fi), tendrías que cambiarla por el nombre correspondiente (por ejemplo, wlan0).  
      
    **--localnet**: Indica que se **debe escanear la red local completa**, basándose en la dirección y máscara de subred configuradas en la interfaz seleccionada.
    
      
    
    ![[Imagenes/image 1 4.png|image 1 4.png]]
    

**NOTA:** Como truco que una **MAC** comience como **08:00:** identifica a una maquina virtual.

  

**NOTA2:** Podemos saber si una maquina es Windows o Linux mediante el comando Ping.

El comando ping se utiliza para verificar la conectividad entre tu dispositivo y otro en la red local o remota.

```Bash
ping -c 1 192.168.1.38
```

  
**Parámetros explicados**  
  
**ping**: Es la **herramienta** que realiza la prueba de conectividad mediante paquetes ICMP.  
  
**-c 1**: Este parámetro especifica **la cantidad de paquetes que se enviarán**. En este caso, el valor 1 indica que solo se enviará un paquete.  
  
**192.168.1.38**: Es la **dirección IP** del destino al que se envía el paquete.  
  

![[Imagenes/image 2 3.png|image 2 3.png]]

  

- Como vemos el **ttl** devuelto es **128**. Este valor suele ser respondido por máquinas **Windows**.
    
    Si el valor fuera **127** indica que el paquete **pasó por al menos un router** antes de llegar.
    
- Un valor de **64** indica que probablemente el origen del paquete es un dispositivo basado en **Linux**.
    
    Si el valor fuera **63** indica que el paquete **pasó por al menos un router** antes de llegar.
    
- **255**: Utilizado en ciertos **dispositivos de red** (routers) o configuraciones especiales.

Por tanto permite estimar el tipo de dispositivo que originó el paquete y cuántos routers o saltos hay entre el origen y destino.

  

- **Herramienta NETDISCOVER**
    
      
    
    ```Bash
    sudo netdiscover -i eth0 -r 192.168.1.0/24 
    ```
    
    ![[Imagenes/image 3 2.png|image 3 2.png]]
    

![[Imagenes/image 4 2.png|image 4 2.png]]

  
**Propósito**  
  
Netdiscover escanea la red en el rango especificado y muestra una lista de dispositivos activos. Este comando es especialmente útil para redes locales donde no se utiliza un servidor DHCP.  
  
**Explicación de los parámetros**  
  
**netdiscover**: Es la **herramienta** que realiza el escaneo de ARP en la red.  
  
**-i eth0**: Especifica **la interfaz de red a usar**, en este caso eth0 (Ethernet). Si estás usando Wi-Fi, puedes cambiarlo por el nombre de la interfaz correspondiente (como wlan0).  
  
**-r 192.168.1.0/24**: **Define el rango de IP a escanear**. Aquí, 192.168.1.0/24 indica que se escaneará toda la subred (de 192.168.1.1 a 192.168.1.254), común en redes privadas.

  

- **Herramienta NMAP**
    
      
    
    nmap -sn 192.168.1.0/24
    
      
    
    ![[Imagenes/image 5 2.png|image 5 2.png]]
    
      
    
    Si queremos guardar el resultado del escaneo de red en un archivo lo escribiríamos así:
    
    nmap -sn 192.168.1.0/24 -oN ip_disponibles.txt
    
    ![[Imagenes/image 6 2.png|image 6 2.png]]
    
      
    
    **Explicación de los parámetros**
    
    > [!important] **-sn**: Este parámetro indica que Nmap **realizará un escaneo de ping ("ping scan") únicamente.** Esto significa que **no se realizarán escaneos de puertos** en los hosts, sino que simplemente verificará qué dispositivos están activos en la red especificada.<br><br>**192.168.1.0/24**: Esta es la **dirección de red** que deseas escanear.<br><br>El formato **/24** indica una **máscara de subred,** que en este caso cubre todas las direcciones IP desde 192.168.1.0 hasta 192.168.1.255. Es decir, estás escaneando un rango completo de IPs en esa subred.<br><br>**-oN ip_disponibles.txt**: Este parámetro **guarda los resultados del escaneo en un archivo llamado ip_disponibles.txt** en un formato normal (legible). Esto te permite analizar más tarde qué hosts están activos.<br><br><br>