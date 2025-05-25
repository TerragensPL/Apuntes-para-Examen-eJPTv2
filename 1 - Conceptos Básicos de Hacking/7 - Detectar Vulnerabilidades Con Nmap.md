# Detectar Vulnerabilidades Con Nmap

Vamos a detectar vulnerabilidades existentes con Nmap o al menos las mas conocidas.

- Hacemos un escaneo de la red local
    
    ```bash
     sudo arp-scan -I eth0 --localnet 
    ```
    
    ![image.png](./imagenes/image%2023.png)
    

- Hacemos un Ping hacia nuestro objetivo para intuir que sistema operativo corre en el .
    
    ```bash
    ping -c 1 192.168.1.38
    ```
    
    ![image.png](./imagenes/image%2024.png)
    

- Vamos a hacer que nmap nos muestre los puertos abiertos y los que este corriendo tras ellos.

```bash
nmap -p- --open -sS -sV --min-rate 1500 -n -vvv -Pn 192.168.1.41 -oN escaneo.txt
```

![image.png](./imagenes/image%2021.png)

Como vemos el puerto 445 nos muestra mucha información y suele ser el del protocolo SMB. Que el Windows 7 era muy propenso a ataque sobre todo si tiene la versión 1 de SMB.

- Vamos a realizar un escaneo de vulnerabilidades con nmap sobre el puerto 445. Hay que tener en cuenta que el script de vulnerabilidades de nmap hace mucho ruido y no es adecuado para utilizarlo en entornos reales.
    
    ```bash
    nmap --script "vuln" -p445 192.168.1.38
    ```
    
    Este comando realiza un escaneo en el puerto 445 del dispositivo con la IP 192.168.1.38, buscando vulnerabilidades relacionadas con el protocolo SMB, como problemas de autenticación, exploits conocidos o configuraciones inseguras.
    
    <aside>
    
    **`nmap`**: Es la **herramienta** principal que estás utilizando para realizar un escaneo de red.
    
    **`--script "vuln"`**: Indica que se **empleará un conjunto de scripts** del motor NSE (Nmap Scripting Engine) **enfocados en la detección de vulnerabilidades**. El script vuln ejecuta varios análisis para identificar posibles vulnerabilidades en el sistema objetivo.
    
    **`-p445`**: Especifica que **solo se escaneará el puerto 445**, el cual, como mencioné antes, está asociado con el protocolo SMB en sistemas Windows.
    
    **`192.168.1.38`**: Es la **dirección IP del sistema objetivo** que deseas analizar.
    
    </aside>
    
    ![image.png](./imagenes/image%2025.png)
    

No encuentra una vulnerabilidad en el puerto y **nos muestra el CVE** de la vulnerabilidad. Aqui entraría en juego la fase de explotación, buscando sploit o herramientas en internet para atacarla manual mente o mediante herramientas automatizadas como  **Metasploit** .

En la siguiente web podemos buscar información sobre el CVE: [**NVD - Home**](./imagenes/https://nvd.nist.gov/)

![image.png](./imagenes/image%2026.png)

Esta en concreto es una de las vulnerabilidades relacionadas con el exploit **EternalBlue**, que fue utilizado en ataques masivos como WannaCry.