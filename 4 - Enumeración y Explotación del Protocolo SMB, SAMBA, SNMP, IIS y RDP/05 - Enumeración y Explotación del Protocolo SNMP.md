# Enumeración y Explotación del Protocolo SNMP

Maquinas que vamos a utilizar **Kali Linux y Chain** [https://vulnyx.com/#chain](https://vulnyx.com/#chain)

<aside>
💡

El Protocolo SNMP (Simple Network Management Protocol) es un estándar utilizado para la gestión y supervisión de dispositivos en una red, como servidores, switches, impresoras y más. Facilita la recopilación de información sobre el estado y rendimiento de los dispositivos, así como la configuración remota.

</aside>

El protocolo **SNMP funciona con UDP**, por lo que es posible que si hacemos un **escaneo de puertos con nmap por UDP** nos encontremos con este protocolo funcionando dentro de la máquina objetivo, por lo que para enumerarlo, utilizaremos **herramientas** como **onesixtyone y snmpwalk**.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2039.png)

- Vamos a hacer un escaneo de puerto que van bajo el **protocolo UDP**.
    
    ```bash
    sudo nmap -sU --top-ports 200 --min-rate 5000 -n -Pn 10.0.3.18
    ```
    
    ![image.png](./imagenes/image%2040.png)
    

<aside>
💡

Si nos encontramos con este protocolo debemos pensar siempre en utilizar la herramienta **onesixtyone y y snmpwalk.**

</aside>

- Vamos a utilizar **onesixtyone**
    
    ```bash
    sudo onesixtyone -c /usr/share/wordlists/rockyou.txt 10.0.3.18
    ```
    
    ![image.png](./imagenes/image%2041.png)
    
    <aside>
    💡
    
    `onesixtyone`: Es la herramienta que estás ejecutando, diseñada para escanear dispositivos en busca de vulnerabilidades relacionadas con SNMP.
    
    `-c /usr/share/wordlists/rockyou.txt`: Especifica un archivo de lista de palabras (en este caso, el famoso archivo "rockyou.txt") que se utiliza para realizar ataques de fuerza bruta contra las cadenas de comunidad SNMP.
    
    `10.0.3.18`: Es la dirección IP del objetivo que deseas escanear.
    
    Este comando intenta identificar cadenas de comunidad SNMP válidas. Las **cadenas de comunidad SNMP** (Simple Network Management Protocol) son una especie de "**contraseñas**" que se utilizan **para controlar el acceso a los dispositivos administrados mediante SNMP**, como routers, switches o impresoras de red. Estas cadenas actúan como una capa de autenticación entre el gestor SNMP (el sistema que solicita información) y el agente SNMP (el dispositivo que proporciona información).
    
    </aside>
    
    Ya tenemos una **cadena de comunidad**. “**security**”
    
- Utilizamos la **herramienta snmpwalk**.
    
    ```bash
    snmpwalk -v 2c -c security 10.0.3.18
    ```
    
    ![image.png](./imagenes/image%2042.png)
    
    Vemos que nos sale un montón de líneas entre ellas podemos encontrar un dominio alguna contraseña, etc.. ese sira el objetivo.
    
    Por ejemplo vemos a qui un dominio y un posible usuario.
    
    ![image.png](./imagenes/image%2043.png)
    
- Vamos a meter este dominio dentro del archivo **/etc/hosts**
    
    ```bash
    sudo nano /etc/hosts
    ```
    
    ![image.png](./imagenes/image%2044.png)
    

- Vamos al navegador web y  escribimos la siguiente **URL http://chaincorp.nyx/**
    
    ![image.png](./imagenes/image%2045.png)
    
- Comprobamos si tiene subdominios con **wfuzz**.
    
    ```bash
    wfuzz -c --hc=404  -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.chaincorp.nyx" -u 10.0.3.18
    ```
    
    ![image.png](./imagenes/image%2046.png)
    
    Como vemos que se repiten sin parar resultados , vamos a poner un delimitador para filtrar, por ejemplo de línea 11. añadiendo al comando `--hl=11`
    
    ```bash
    wfuzz -c --hc=404 --hl=11 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.chaincorp.nyx" -u 10.0.3.18
    ```
    
    ![image.png](./imagenes/image%2047.png)
    

- Probamos el subdominio que hemos encontrado. Lo metemos del archivo **/etc/hosts**
    
    ```bash
    sudo nano /etc/hosts
    ```
    
    ![image.png](./imagenes/image%2048.png)
    
    En el navegaor escribimos `http://utils.chaincorp.nyx/`
    
    ![image.png](./imagenes/image%2049.png)