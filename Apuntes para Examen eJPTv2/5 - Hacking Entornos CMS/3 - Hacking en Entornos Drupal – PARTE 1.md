Maquinas a utilizar **Kali Linux y FAKE NEWS (User)** (https://ctf.comunidadhackingetico.es/challenges)

  

**Drupal es un CMS** muy utilizado a día de hoy, tratándose de una **alternativa a WordPress**, por lo que también existen técnicas de explotación y vulnerabilidades en este tipo de CMS.  
En esta ocasión, vamos a ver cómo detectar la versión de un CMS Drupal y cual es su principal vulnerabilidad.  

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 53.png|image 53.png]]

  

```Bash
ping -c 1 10.0.3.20  
```

![[Imagenes/image 1 42.png|image 1 42.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.20 
```

![[Imagenes/image 2 41.png|image 2 41.png]]

Vemos que en el puerto 80 corre un servidor Apache pero además un Drupal.

  

- Nos vamos al navegador web y miramos que corre por el puerto 80.
    
    ![[Imagenes/image 3 40.png|image 3 40.png]]
    

  

- Cuando hemos hecho el nmap nos a mostrado que tenia Drupal pero nos lo muestra en forma de directorio. Vamos a probar cargar ese directorio haber si nos lleva a alguna parte.
    
    `10.0.3.20/drupal-7.57/`
    
    ![[Imagenes/image 4 38.png|image 4 38.png]]
    
    A tardado un poco pero hemos visto que intentaba resolver la dirección URL y por tanto hemos esperado si la resolvía. Si no resolvía podíamos haber intentado hace un **Fuzzing Web.**
    
      
    
- Comprobamos la versión del Grupal , en este caso ya la sabemos la 7.57 , pero vamos a comprobarla. **Pulsamos CTRL+U** para mirar el código fuente.
    
    ![[Imagenes/image 5 37.png|image 5 37.png]]
    
    Vemos que tenemos muchos indicios que confirman la versión del Drupal.
    
      
    
- Lanzamos el Metasploit
    
    ![[Imagenes/image 6 35.png|image 6 35.png]]
    
    ![[Imagenes/image 7 33.png|image 7 33.png]]
    
      
    
    - Buscamos posibles exploits para Drupal.
        
        ![[Imagenes/image 8 31.png|image 8 31.png]]
        
        Ya adelanto que la versión 7.57 de Drupal es vulnerable a un exploit llamado drupal_drupalgeddon2
        
    - Utilizamos el exploit 1
        
        `use 1`
        
        ![[Imagenes/image 9 28.png|image 9 28.png]]
        
          
        
        `show options`
        
        ![[Imagenes/image 10 24.png|image 10 24.png]]
        
          
        
        Rellenamos las opciones que nos falten y las que estén ya puestas sean correctas. Como nota a tener en cuenta que si el servidor web estuviera corriendo por un puerto diferente al 80 deberíamos indicarlo en RPORT.
        
          
        
        `set RHOSTS` `[http://10.0.3.20/drupal-7.57/](http://10.0.3.20/drupal-7.57/)`
        
        ![[Imagenes/image 11 18.png|image 11 18.png]]
        
          
        
        `show options`
        
        ![[Imagenes/image 12 18.png|image 12 18.png]]
        
          
        
        `run`
        
        ![[Imagenes/image 13 17.png|image 13 17.png]]
        
        Nos a abierto una sesión de Meterpreter. El Meterpreter es muy útil para hacer pivoting y cosas avanzadas con metasploit pero si queremos navegar por los directorios , ver el contenido de los archivo, subir privilegios es recomendable cambiar de Shell.
        
        `shell`
        
        `bash -i`
        
        ![[Imagenes/image 14 15.png|image 14 15.png]]
        
        **Shell** nos cambiaria a una terminal de Linux y el comando **bash -i** nos daría un prompt.