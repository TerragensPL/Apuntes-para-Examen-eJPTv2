Vamos a detectar vulnerabilidades existentes con Nmap o al menos las mas conocidas.

  

- Hacemos un escaneo de la red local
    
    ```Bash
     sudo arp-scan -I eth0 --localnet 
    ```
    
    ![[Imagenes/image 9.png|image 9.png]]
    

  

- Hacemos un Ping hacia nuestro objetivo para intuir que sistema operativo corre en el .
    
    ```Bash
    ping -c 1 192.168.1.38
    ```
    
    ![[Imagenes/image 1 6.png|image 1 6.png]]
    

  

- Vamos a hacer que nmap nos muestre los puertos abiertos y los que este corriendo tras ellos.

```Bash
nmap -p- --open -sS -sV --min-rate 1500 -n -vvv -Pn 192.168.1.41 -oN escaneo.txt
```

![[Imagenes/image 5 3.png|image 5 3.png]]

Como vemos el puerto 445 nos muestra mucha información y suele ser el del protocolo SMB. Que el Windows 7 era muy propenso a ataque sobre todo si tiene la versión 1 de SMB.

  

- Vamos a realizar un escaneo de vulnerabilidades con nmap sobre el puerto 445. Hay que tener en cuenta que el script de vulnerabilidades de nmap hace mucho ruido y no es adecuado para utilizarlo en entornos reales.
    
    ```Bash
    nmap --script "vuln" -p445 192.168.1.38
    ```
    
      
    Este comando realiza un escaneo en el puerto 445 del dispositivo con la IP 192.168.1.38, buscando vulnerabilidades relacionadas con el protocolo SMB, como problemas de autenticación, exploits conocidos o configuraciones inseguras.  
    
      
    
    > [!important] `**nmap**`: Es la **herramienta** principal que estás utilizando para realizar un escaneo de red.<br><br><br>`**--script "vuln"**`: Indica que se **empleará un conjunto de scripts** del motor NSE (Nmap Scripting Engine) **enfocados en la detección de vulnerabilidades**. El script vuln ejecuta varios análisis para identificar posibles vulnerabilidades en el sistema objetivo.<br><br><br>`**-p445**`: Especifica que **solo se escaneará el puerto 445**, el cual, como mencioné antes, está asociado con el protocolo SMB en sistemas Windows.<br><br><br>`**192.168.1.38**`: Es la **dirección IP del sistema objetivo** que deseas analizar.
    
      
    
    ![[Imagenes/image 2 5.png|image 2 5.png]]
    

No encuentra una vulnerabilidad en el puerto y **nos muestra el CVE** de la vulnerabilidad. Aqui entraría en juego la fase de explotación, buscando sploit o herramientas en internet para atacarla manual mente o mediante herramientas automatizadas como **Metasploit** .

En la siguiente web podemos buscar información sobre el CVE: **[NVD - Home](https://nvd.nist.gov/)**

![[Imagenes/image 3 4.png|image 3 4.png]]

Esta en concreto es una de las vulnerabilidades relacionadas con el exploit **EternalBlue**, que fue utilizado en ataques masivos como WannaCry.