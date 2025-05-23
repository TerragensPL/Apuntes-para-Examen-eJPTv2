Maquinas que utilizaremos Kali Linux y Jenk([https://vulnyx.com/#jenk](https://vulnyx.com/#jenk))

### **NOTA: No es lo mismo la búsqueda de subdirectorios web que es lo que vamos a hacer en este caso que hacerlo de subdominios aunque podamos utilizar las mismas herramientas.**

![[Imagenes/image 30.png|image 30.png]]

  

Vamos a ver alternativas alternativas Gobuster para realizar fuzzing web.

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 1 19.png|image 1 19.png]]

  

```Bash
 ping -c 1 10.0.3.12
```

![[Imagenes/image 2 18.png|image 2 18.png]]

  

```Bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.12
```

![[Imagenes/image 3 17.png|image 3 17.png]]

Vemos que tiene abierto el puerto 80. vamos a probarlo en el navegador web.

![[Imagenes/image 4 16.png|image 4 16.png]]

  

- Vamos a a comenzar con el **Fuzzing Web**. Y comenzaremos utilizando **wfuzz.**
    
    ```Bash
    wfuzz -c --hc=404 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://10.0.3.12/FUZZ
    ```
    
    ![[Imagenes/image 5 15.png|image 5 15.png]]
    
      
    
    Wfuzz, es una herramienta de fuerza bruta para encontrar recursos ocultos en servidores web. Aquí te explico los parámetros:
    
    - `**c**`: Activa la salida en color para que los resultados sean más fáciles de leer.
    - `**-hc=404**`: Indica que se deben ocultar las respuestas HTTP con el código de estado 404 (Not Found), ya que no son relevantes. Si por ejemplo quiero ocultar los códigos de estado 403 solo debería añadir **- - hc=403**
    - `**w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt**`: Especifica el archivo de lista de palabras (wordlist) que se usará para realizar las pruebas. En este caso, se utiliza una lista de directorios y archivos comunes.
    - `**http://10.0.3.12/FUZZ**`: Es la URL objetivo. La palabra clave `FUZZ` será reemplazada por cada entrada de la wordlist durante el ataque de fuerza bruta.
        
          
        
    
    Hemos encontrado “webcams”, vamos a probarlo en el navegador web.
    
    ![[Imagenes/image 6 14.png|image 6 14.png]]
    
      
    
      
    
    - Otra herramienta seria **dirb.**
        
        ```Bash
        dirb http://10.0.3.12/
        ```
        
        ![[Imagenes/image 7 12.png|image 7 12.png]]
        
          
        
        Dirb es muy rápida y sencilla pero su mayor **problema** es que usa un d**iccionario con pocas palabras** claves 4612. Por tanto es **aconsejable usar otras herramientas junto con dirb**.
        
    
      
    
    - También tenemos **dirsearch**
        
        Si no la tenemos instalada podemos hacerlo de la siguiente manera.
        
        ```Bash
        sudo apt install dirsearch 
        ```
        
        ![[Imagenes/image 8 11.png|image 8 11.png]]
        
          
        
        Lo **ejecutamos de manera básica** con su diccionario por defecto:
        
        ```Bash
        dirsearch -u http://10.0.3.12/
        ```
        
        ![[Imagenes/image 9 10.png|image 9 10.png]]
        
          
        
        **Le damos un diccionario para que lo utilice**:
        
        ```Bash
        dirsearch -u http://10.0.3.12/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
        ```
        
        ![[Imagenes/image 10 10.png|image 10 10.png]]