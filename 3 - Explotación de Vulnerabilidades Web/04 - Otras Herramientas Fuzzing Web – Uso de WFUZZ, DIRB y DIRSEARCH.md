# Otras Herramientas Fuzzing Web – Uso de WFUZZ, DIRB y DIRSEARCH

Maquinas que utilizaremos Kali Linux y Jenk([https://vulnyx.com/#jenk](https://vulnyx.com/#jenk))

### **NOTA: No es lo mismo la búsqueda de subdirectorios web que es lo que vamos a hacer en este caso que hacerlo de subdominios aunque podamos utilizar las mismas herramientas.**

![image.png](./imagenes/image%2027.png)

Vamos a ver alternativas  alternativas Gobuster para realizar fuzzing web.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2028.png)

```bash
 ping -c 1 10.0.3.12
```

![image.png](./imagenes/image%2029.png)

```bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.12
```

![image.png](./imagenes/image%2030.png)

Vemos que tiene abierto el puerto 80. vamos a probarlo en el navegador web.

![image.png](./imagenes/image%2031.png)

- Vamos a a comenzar con el **Fuzzing Web**. Y comenzaremos utilizando **wfuzz.**
    
    ```bash
    wfuzz -c --hc=404 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt [http://10.0.3.12/FUZZ](./imagenes/http://10.0.3.12/FUZZ)
    ```
    
    ![image.png](./imagenes/image%2032.png)
    
    Wfuzz, es una herramienta de fuerza bruta para encontrar recursos ocultos en servidores web. Aquí te explico los parámetros:
    
    - **`c`**: Activa la salida en color para que los resultados sean más fáciles de leer.
    - **`-hc=404`**: Indica que se deben ocultar las respuestas HTTP con el código de estado 404 (Not Found), ya que no son relevantes. Si por ejemplo quiero ocultar los códigos de estado 403 solo debería añadir **- - hc=403**
    - **`w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt`**: Especifica el archivo de lista de palabras (wordlist) que se usará para realizar las pruebas. En este caso, se utiliza una lista de directorios y archivos comunes.
    - **`http://10.0.3.12/FUZZ`**: Es la URL objetivo. La palabra clave `FUZZ` será reemplazada por cada entrada de la wordlist durante el ataque de fuerza bruta.
        
        
    
    Hemos encontrado “webcams”, vamos a probarlo en el navegador web.
    
    ![image.png](./imagenes/image%2033.png)
    
    - Otra herramienta seria **dirb.**
        
        ```bash
        dirb [http://10.0.3.12/](./imagenes/http://10.0.3.12/)
        ```
        
        ![image.png](./imagenes/image%2034.png)
        
        Dirb es muy rápida y sencilla pero su mayor **problema** es que usa un d**iccionario con pocas palabras** claves 4612. Por tanto es **aconsejable usar otras herramientas junto con dirb**.
        
    
    - También tenemos **dirsearch**
        
        Si no la tenemos instalada podemos hacerlo de la siguiente manera.
        
        ```bash
        sudo apt install dirsearch 
        ```
        
        ![image.png](./imagenes/image%2035.png)
        
        Lo **ejecutamos de manera básica** con su diccionario por defecto:
        
        ```bash
        dirsearch -u [http://10.0.3.12/](./imagenes/http://10.0.3.12/)
        ```
        
        ![image.png](./imagenes/image%2036.png)
        
        **Le damos un diccionario para que lo utilice**:
        
        ```bash
        dirsearch -u [http://10.0.3.12/](./imagenes/http://10.0.3.12/) -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
        ```
        
        ![image.png](./imagenes/image%2037.png)