Maquinas a utilizar Kali Linux y Hunter ([https://vulnyx.com/#hunter](https://vulnyx.com/#hunter))

El **protocolo DNS** (Sistema de Nombres de Dominio) es esencial para el funcionamiento de Internet. Su propósito principal es traducir nombres de dominio legibles por humanos (como `www.ejemplo.com`) en direcciones IP (como `192.168.1.1`), que son necesarias para que los dispositivos se comuniquen entre sí en la red.

  

Un **ataque de transferencia de zona DNS** ocurre cuando un atacante explota una mala configuración en un servidor DNS para obtener información sensible sobre una red. La transferencia de zona DNS (también conocida como **AXFR**) es un mecanismo legítimo utilizado para sincronizar datos entre servidores DNS primarios y secundarios. Sin embargo, si no está configurada correctamente, puede ser aprovechada por atacantes.

### ¿Cómo funciona?

1. **Transferencia de zona legítima:** Los servidores DNS secundarios solicitan al servidor primario una copia de la base de datos DNS (la "zona") para mantenerse actualizados.
2. **Ataque:** Si el servidor DNS primario no restringe quién puede realizar una transferencia de zona, un atacante puede enviar una solicitud AXFR y obtener una copia completa de la base de datos DNS.

### ¿Qué información puede obtener un atacante?

- Subdominios.
- Direcciones IP internas.
- Configuración de servidores.
- Información sobre la infraestructura de la red.

### ¿Cómo prevenirlo?

- Configurar el servidor DNS para permitir transferencias de zona solo a servidores autorizados.
- Usar firewalls para limitar el acceso a los servidores DNS.
- Monitorear y auditar solicitudes de transferencia de zona.

  

---

- Vamos a ver como podemos enumerar básicamente el el protocolo DNS que esta funcionando en una maquina por el p**uerto 53 que es el de por defecto** del protocolo **DNS**.
    
    ```Bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ```Bash
    ping -c 1 10.0.3.11
    ```
    
    ![[Imagenes/image 29.png|image 29.png]]
    
      
    
    ```Bash
    nmap -p- -sS -sV -sC --open -min-rate 5000 -n -vvv -Pn 10.0.3.1
    ```
    
    ![[Imagenes/image 1 18.png|image 1 18.png]]
    

Algo que **debemos tener siempre en cuenta**, siempre que estemos enumerando con cualquier herramienta **uno de los objetivos** que debemos tener en todo momento **es encontrar Dominios**. Si encontramos Dominios luego podemos seguir tirando de ese hilo puedo estar enumerando , puedo acceder a ese dominio puedo encontrar subdominios que vimos con Wfuzz.

Apuntamos el dominio encontrado: **hunterzone.nyx**

- Editamos el archivo **/etc/hosts** y añadimos el dominio que hemos encontrado.
    
    ```Bash
    sudo nano /etc/hosts 
    ```
    
    ![[Imagenes/image 2 17.png|image 2 17.png]]
    

- ¿Qué pasa cuando tengo un dominio y el protocolo DNS funcionando como es en nuestro caso?. Cuando ocurre esto podemos hacer ataque de transferencia de zona , que nos permite obtener mucha información de la zona DNS de este servidor (encontrando subdominios y mas información sensible).
    
    ![[Imagenes/image 1 18.png|image 1 18.png]]
    
      
    
- Vamos a utilizar la herramienta **dig**.
    
    ```Bash
    dig axfr hunterzone.nyx @10.0.3.11
    ```
    
    ![[Imagenes/image 3 16.png|image 3 16.png]]
    
    ### Explicación de los elementos:
    
    1. `**dig**`: Es la herramienta para realizar consultas de DNS. Se utiliza para obtener información de servidores DNS sobre dominios y registros.
    2. `**axfr**`: Es una consulta de transferencia completa de zona DNS (AXFR, por sus siglas en inglés). Se utiliza para solicitar la transferencia de todos los registros de una zona DNS desde un servidor autoritativo. Es un tipo de operación que se suele usar en la configuración de servidores DNS secundarios para replicar registros desde el servidor principal.
    3. `**hunterzone.nyx**`: Este es el nombre del dominio o zona que deseas consultar. En este caso, parece que estás intentando realizar una transferencia de la zona llamada "hunterzone.nyx".
    4. `**@10.0.3.11**`: Esto especifica la dirección IP del servidor DNS al que estás enviando la consulta. En este caso, estás solicitando la transferencia de zona desde el servidor DNS localizado en `10.0.3.11`.
    
    ### Opciones y advertencias:
    
    - El comando intenta obtener todos los registros de la zona de manera completa. Sin embargo, no todos los servidores DNS permiten transferencias de zona (AXFR) debido a preocupaciones de seguridad. Por lo general, esta operación está restringida solo a servidores DNS autorizados.
    - Si el servidor `10.0.3.11` no permite transferencias de zona o no está configurado correctamente, la operación fallará y puede devolver un error.
    
      
    
- Tendríamos que apuntar los subdominios en el archivo **/etc/hosts** y investigarlos. En nuestro caso solo investigaremos **devhunter.nyx.**
    
    ```Bash
    sudo nano /etc/hosts
    ```
    
    ![[Imagenes/image 4 15.png|image 4 15.png]]
    
      
    
    En el navegador veríamos esto con el nuevo dominio **devhunter.nyx**:
    
    ![[Imagenes/image 5 14.png|image 5 14.png]]
    

- Vamos a enumerar subdominios de **devhunter.nyx.**
    
    Si no tenemos el diccionario que vamos a usar podemos encontrarlo en este repositorio:
    
    [GitHub - danielmiessler/SecLists: SecLists es el compañero del probador de seguridad. Es una colección de varios tipos de listas que se utilizan durante las evaluaciones de seguridad, recopiladas en un solo lugar. Los tipos de lista incluyen nombres de usuario, contraseñas, URL, patrones de datos confidenciales, cargas útiles de fuzzing, shell web y muchos más.](https://github.com/danielmiessler/SecLists)
    
    O instalándolas directamente en nuestra Kali.
    
    ```Bash
    sudo apt install seclists
    ```
    
    ```Bash
    wfuzz -c --hc=404 --hl=367 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "Host: FUZZ.devhunter.nyx" -u 10.0.3.11 
    ```
    
    ![[Imagenes/image 6 13.png|image 6 13.png]]
    
    Hemos en encontrado un nuevo subdominio llamado “files.
    
    ### Comando:
    
    1. `**c**`: Este parámetro indica que la salida en la consola será en color. Ayuda a distinguir fácilmente entre diferentes resultados.
    2. `**-hc=404**`: Este parámetro **ignora las respuestas con el código de estado HTTP 404** (Not Found). Esto es útil para evitar mostrar resultados irrelevantes donde no hay recursos disponibles.
    3. `**-hl=367**`: Este parámetro **ignora las respuestas cuyo contenido tenga una longitud de 367 caracteres**. Se utiliza para filtrar resultados repetitivos o irrelevantes, asegurando que solo se muestren los datos relevantes.
    4. `**w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt**`: Con este parámetro, se especifica un **archivo de wordlist**. En este caso, es una lista de palabras utilizada para probar diferentes subdominios. El archivo está ubicado en la ruta indicada.
    5. `**H "Host: FUZZ.devhunter.nyx"**`: Aquí se define un **encabezado HTTP personalizado**. El término `FUZZ` indica el lugar donde wfuzz insertará las palabras del wordlist para realizar pruebas, reemplazando `FUZZ` con cada palabra del archivo en las solicitudes.
    6. `**u 10.0.3.11**`: Este parámetro define la **URL objetivo**. En este caso, la herramienta wfuzz realizará solicitudes al servidor ubicado en `10.0.3.11`.
    
      
    
    Editamos de nuevo **/etc/hosts**
    
    ```Bash
    sudo nano /etc/hosts
    ```
    
    ![[Imagenes/image 7 11.png|image 7 11.png]]
    
      
    
    Probamos a acceder a este subdominio en el navegador web.
    
    ![[Imagenes/image 8 10.png|image 8 10.png]]