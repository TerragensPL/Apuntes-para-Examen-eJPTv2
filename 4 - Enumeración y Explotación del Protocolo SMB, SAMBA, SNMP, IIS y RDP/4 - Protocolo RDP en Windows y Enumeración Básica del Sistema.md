Maquinas a usar **Kali Linux y Laboratorio de Try Hack Me Investigatin Windows.**([https://tryhackme.com/room/investigatingwindows](https://tryhackme.com/room/investigatingwindows))

  

> [!important] El **Protocolo RDP** (Remote Desktop Protocol) en Windows permite a los usuarios **conectarse de forma remota y grafica** a otros equipos, brindando acceso a su escritorio y aplicaciones como si estuvieran físicamente presentes. Es muy utilizado para administración remota y soporte técnico.<br>Por defecto, RDP utiliza el **puerto 3389** para establecer la conexión. Sin embargo, este puerto puede cambiarse manualmente por razones de seguridad o configuración personalizada. <br><br>

  

Vamos a **suponer** que hemos **conseguido usuario y contraseña** de un usuario en **Windows** y que veamos que el **puerto 3389** esta **abierto**.

**Username: Administrator**  
**Password: letmein123!**  

  

```Bash
sudo nmap -p- -sS -sV -sC --open -min-rate 5000 -n -vvv -Pn 10.10.220.216
```

![[Imagenes/image 46.png|image 46.png]]

  

- Usaríamos la herramienta **xfreerdp.**
    
    En mi caso la herramienta no la puedo emplear ya que no esta en Kali y no tengo opciones de instalarla pero dejo como seria el comando.
    
    ```Bash
    xfreerdp /u:Administrator /p:letmein123! /v:10.10.220.216:3389
    ```
    

  

- Voy a usar la herramienta **xfreerdp3**
    
    ```Bash
    xfreerdp3 /u:Administrator /p:letmein123! /v:10.10.220.216:3389
    ```
    
    ![[Imagenes/image 1 35.png|image 1 35.png]]
    
      
    
    > [!important] `**xfreerdp3**`: Es la herramienta que estás usando, una versión específica de FreeRDP.
    > 
    > `**/u:Administrator**`: Indica el nombre de usuario (en este caso, "Administrator").
    > 
    > `**/p:letmein123!**`: Es la contraseña asociada al usuario.
    > 
    > `**/v:10.10.220.216:3389**`: Especifica la dirección IP del servidor al que deseas conectarte y el puerto RDP (3389 es el estándar para conexiones RDP).
    
    > [!important] **OJO** el usuario de la maquina remota vería lo que estamos haciendo.
    
      
    
    - Para saber información sobre la maquina.
        
        **about→ Clip derecho propiedades**
        
        ![[Imagenes/image 2 34.png|image 2 34.png]]
        
        ![[Imagenes/image 3 33.png|image 3 33.png]]
        
        ![[Imagenes/image 4 32.png|image 4 32.png]]
        
    
      
    
    - Saber que usuarios hay en la maquina **abrimos el CMD**.
        
        ![[Imagenes/image 5 31.png|image 5 31.png]]
        
    
      
    
    - Lista usuarios de la maquina.
        
        `net user`
        
        ![[Imagenes/image 6 29.png|image 6 29.png]]
        
    
      
    
    - Que usuario soy
        
        `whoami`
        
        ![[f8d03713-b46a-4aef-9112-6f31412d3cde.png]]
        
    
      
    
    - Información del usuario John
        
        `net user John`
        
        ![[Imagenes/image 7 27.png|image 7 27.png]]
        
    
      
    
    - Lista los usuarios dentro del grupo administrador
        
        `**net localgroup Administrators**`
        
        ![[Imagenes/image 8 25.png|image 8 25.png]]