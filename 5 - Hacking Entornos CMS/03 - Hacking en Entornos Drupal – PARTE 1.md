# Hacking en Entornos Drupal – PARTE 1

Maquinas a utilizar **Kali Linux y FAKE NEWS (User)** (https://ctf.comunidadhackingetico.es/challenges)

**Drupal es un CMS** muy utilizado a día de hoy, tratándose de una **alternativa a WordPress**, por lo que también existen técnicas de explotación y vulnerabilidades en este tipo de CMS.
En esta ocasión, vamos a ver cómo detectar la versión de un CMS Drupal y cual es su principal vulnerabilidad.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2032.png)

```bash
ping -c 1 10.0.3.20  
```

![image.png](./imagenes/image%2033.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.20 
```

![image.png](./imagenes/image%2034.png)

Vemos que en el puerto 80 corre un servidor Apache pero además un Drupal.

- Nos vamos al navegador web y miramos que corre por el puerto 80.
    
    ![image.png](./imagenes/image%2035.png)
    

- Cuando hemos hecho el nmap nos a mostrado que tenia Drupal pero nos lo muestra en forma de directorio. Vamos a probar cargar ese directorio haber si nos lleva a alguna parte.
    
    `10.0.3.20/drupal-7.57/`
    
    ![image.png](./imagenes/image%2036.png)
    
    A tardado un poco pero hemos visto que intentaba resolver la dirección URL y por tanto hemos esperado si la resolvía. Si no resolvía podíamos haber intentado hace un **Fuzzing Web.**
    
- Comprobamos la versión del Grupal , en este caso ya la sabemos la 7.57 , pero vamos a comprobarla. **Pulsamos CTRL+U** para mirar el código fuente.
    
    ![image.png](./imagenes/image%2037.png)
    
    Vemos que tenemos muchos indicios que confirman la versión del Drupal.
    
- Lanzamos el Metasploit
    
    ![image.png](./imagenes/image%2038.png)
    
    ![image.png](./imagenes/image%2039.png)
    
    - Buscamos posibles exploits para Drupal.
        
        ![image.png](./imagenes/image%2040.png)
        
        Ya adelanto que la versión 7.57 de Drupal es vulnerable a un exploit  llamado drupal_drupalgeddon2
        
    - Utilizamos el exploit 1
        
        `use 1`
        
        ![image.png](./imagenes/image%2041.png)
        
        `show options`
        
        ![image.png](./imagenes/image%2042.png)
        
        Rellenamos las opciones que nos falten y las que estén ya puestas sean correctas. Como nota a tener en cuenta que si el servidor web estuviera corriendo por un puerto diferente al 80 deberíamos indicarlo en RPORT.
        
        `set RHOSTS [http://10.0.3.20/drupal-7.57/](./imagenes/http://10.0.3.20/drupal-7.57/)`
        
        ![image.png](./imagenes/image%2043.png)
        
        `show options`
        
        ![image.png](./imagenes/image%2044.png)
        
        `run`
        
        ![image.png](./imagenes/image%2045.png)
        
        Nos a abierto una sesión de Meterpreter. El Meterpreter es muy útil para hacer pivoting y cosas avanzadas con metasploit pero si queremos navegar por los directorios , ver el contenido de los archivo, subir privilegios es recomendable cambiar de Shell.
        
        `shell`
        
        `bash -i` 
        
        ![image.png](./imagenes/image%2046.png)
        
        **Shell** nos cambiaria a una terminal de Linux y el comando **bash -i** nos daría un prompt.