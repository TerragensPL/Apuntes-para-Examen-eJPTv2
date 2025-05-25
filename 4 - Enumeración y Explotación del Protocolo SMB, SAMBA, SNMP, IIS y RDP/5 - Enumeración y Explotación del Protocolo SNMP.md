Maquinas que vamos a utilizar **Kali Linux y Chain** [https://vulnyx.com/#chain](https://vulnyx.com/#chain)

  

> [!important] El Protocolo SNMP (Simple Network Management Protocol) es un estándar utilizado para la gestión y supervisión de dispositivos en una red, como servidores, switches, impresoras y más. Facilita la recopilación de información sobre el estado y rendimiento de los dispositivos, así como la configuración remota.

  

El protocolo **SNMP funciona con UDP**, por lo que es posible que si hacemos un **escaneo de puertos con nmap por UDP** nos encontremos con este protocolo funcionando dentro de la máquina objetivo, por lo que para enumerarlo, utilizaremos **herramientas** como **onesixtyone y snmpwalk**.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 47.png|image 47.png]]

  

- Vamos a hacer un escaneo de puerto que van bajo el **protocolo UDP**.
    
    ```Bash
    sudo nmap -sU --top-ports 200 --min-rate 5000 -n -Pn 10.0.3.18
    ```
    
    ![[Imagenes/image 1 36.png|image 1 36.png]]
    

> [!important] Si nos encontramos con este protocolo debemos pensar siempre en utilizar la herramienta **onesixtyone y y snmpwalk.**

  

- Vamos a utilizar **onesixtyone**
    
    ```Bash
    sudo onesixtyone -c /usr/share/wordlists/rockyou.txt 10.0.3.18
    ```
    
    ![[Imagenes/image 2 35.png|image 2 35.png]]
    
      
    
    > [!important] `onesixtyone`: Es la herramienta que estás ejecutando, diseñada para escanear dispositivos en busca de vulnerabilidades relacionadas con SNMP.
    > 
    >   
    >   
    > `-c /usr/share/wordlists/rockyou.txt`: Especifica un archivo de lista de palabras (en este caso, el famoso archivo "rockyou.txt") que se utiliza para realizar ataques de fuerza bruta contra las cadenas de comunidad SNMP.  
    >   
    >   
    > `10.0.3.18`: Es la dirección IP del objetivo que deseas escanear.  
    >   
    > Este comando intenta identificar cadenas de comunidad SNMP válidas. Las **cadenas de comunidad SNMP** (Simple Network Management Protocol) son una especie de "**contraseñas**" que se utilizan **para controlar el acceso a los dispositivos administrados mediante SNMP**, como routers, switches o impresoras de red. Estas cadenas actúan como una capa de autenticación entre el gestor SNMP (el sistema que solicita información) y el agente SNMP (el dispositivo que proporciona información).  
    >   
    >   
    
    Ya tenemos una **cadena de comunidad**. “**security**”
    
      
    
- Utilizamos la **herramienta snmpwalk**.
    
    ```Bash
    snmpwalk -v 2c -c security 10.0.3.18
    ```
    
    ![[Imagenes/image 3 34.png|image 3 34.png]]
    
    Vemos que nos sale un montón de líneas entre ellas podemos encontrar un dominio alguna contraseña, etc.. ese sira el objetivo.
    
    Por ejemplo vemos a qui un dominio y un posible usuario.
    
    ![[Imagenes/image 4 33.png|image 4 33.png]]
    
      
    
- Vamos a meter este dominio dentro del archivo **/etc/hosts**
    
    ```Bash
    sudo nano /etc/hosts
    ```
    
    ![[Imagenes/image 5 32.png|image 5 32.png]]
    

- Vamos al navegador web y escribimos la siguiente **URL http://chaincorp.nyx/**
    
    ![[Imagenes/image 6 30.png|image 6 30.png]]
    
      
    
- Comprobamos si tiene subdominios con **wfuzz**.
    
    ```Bash
    wfuzz -c --hc=404  -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.chaincorp.nyx" -u 10.0.3.18
    ```
    
    ![[Imagenes/image 7 28.png|image 7 28.png]]
    
    Como vemos que se repiten sin parar resultados , vamos a poner un delimitador para filtrar, por ejemplo de línea 11. añadiendo al comando `--hl=11`
    
      
    
    ```Bash
    wfuzz -c --hc=404 --hl=11 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.chaincorp.nyx" -u 10.0.3.18
    ```
    
    ![[Imagenes/image 8 26.png|image 8 26.png]]
    

  

- Probamos el subdominio que hemos encontrado. Lo metemos del archivo **/etc/hosts**
    
    ```Bash
    sudo nano /etc/hosts
    ```
    
    ![[Imagenes/image 9 23.png|image 9 23.png]]
    
      
    
    En el navegaor escribimos `http://utils.chaincorp.nyx/`
    
    ![[Imagenes/image 10 20.png|image 10 20.png]]