# Enumeración de Subdominios con Wfuzz

Maquinas que utilizaremos **Kali Linux y Logan (**[https://hackmyvm.eu/](https://hackmyvm.eu/))

Nos puede pasar que veamos abierto el puerto 80  y pongamos la IP en nuestro navegador pero no cargue la pagina esto es por que se aplica **virtual hosting** (es una técnica que permite a un servidor web alojar múltiples sitios web en una misma máquina física. Esto se logra asignando nombres de dominio o direcciones IP específicas a cada sitio web, lo que permite al servidor identificar y enrutar las solicitudes adecuadamente. En esencia, hace que un solo servidor funcione como si fueran varios servidores independientes).

```bash
sudo arp-scan -I eth0 --localnet
```

```bash
ping -c 1 10.0.3.10
```

![image.png](./imagenes/image%207.png)

```bash
nmap -p- -sS -sV -sC -min-rate 5000 -n -vvv -Pn 10.0.3.10 -oN escaneo
```

![image.png](./imagenes/image%208.png)

Nos fijamos que tiene abierto el puerto 80 por el que corre un servidor web apache. Pero también vemos vemos que pone “**Service Info: Host: logan.hmv**” esto nos dice que estamos ante un caso de **virtual hosting.** Para asegurarnos entramos en el navegador web y ponemos la IP de la maquina victima.

![image.png](./imagenes/image%209.png)

Como vemos **nos da un problema a la hora de cargar la pagina pero nos señala un dominio en la URL “logan.hmv” esto nos indica que estamos en un caso de virtual hosting** ya que mi maquina Kali no esta pudiendo asociar la IP de la maquina victima con el logan.hmv.

Para ello vamos a editar el fichero `/etc/hosts` que le indique que el navegador debe apuntar a un determinado dominio.

```bash
sudo nano /etc/hosts
```

![image.png](./imagenes/image%2010.png)

Lo modificamos para que quede de la siguiente manera añadiendo la siguiente línea que indicaría que **la IP de la maquina victima apunte al archivo logan.hmv**.

`10.0.3.10       logan.hmv`

![image.png](./imagenes/image%2011.png)

Ya podemos visualizar la pagina Web.

![image.png](./imagenes/image%2012.png)

Cuando se aplica virtual hosting hay que hacer una comprobación adicional hay que ver si tenemos subdominios disponible dentro del dominio, para ello **utilizaremos la herramienta wfuzz.**

```bash
wfuzz -c --hc=404 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "Host: FUZZ.logan.hmv" -u 10.0.3.10
```

1. **`c`**: Activa la salida en color para que los resultados sean más fáciles de leer.
2. **`-hc=404`**: Indica que se deben ocultar las respuestas HTTP con el código de estado 404 (Not Found). Esto es útil para filtrar resultados irrelevantes.
3. **`w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt`**: Especifica el archivo de wordlist que se usará para las pruebas de fuerza bruta. En este caso, se está utilizando una lista de palabras de DirBuster.
4. **`H "Host: FUZZ.logan.hmv"`**: Añade un encabezado HTTP personalizado. Aquí, la palabra clave `FUZZ` será reemplazada por cada entrada de la wordlist durante la prueba.
5. **`u 10.0.3.10`**: Define la URL objetivo, que en este caso es la dirección IP `10.0.3.10`.

En resumen, este comando está configurado para buscar subdominios o directorios en el servidor `10.0.3.10`, utilizando la wordlist especificada y reemplazando `FUZZ` en el encabezado `Host` con cada entrada de la lista.

![image.png](./imagenes/image%2013.png)

Como vemos nos salen infinitas líneas.  Debemos aplicar filtros para encontrar resultados que nos sirvan. Le vamos a añadir lo siguiente al comando.

`--hl=1`: Filtra las respuestas basándose en el número de líneas. En este caso, solo se mostrarán respuestas que tengan más de 1 línea. Esto **puede ser útil para identificar respuestas más detalladas o significativas**.

El comando quedaría así:

```bash
wfuzz -c --hc=404 --hl=1 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "Host: FUZZ.logan.hmv" -u 10.0.3.10
```

![image.png](./imagenes/image%2014.png)

Vamos a probar lo que nos a encontrado. Escribimos en el navegador web `http://admin.logan.hmv/`

![image.png](./imagenes/image%2015.png)

😲😲 No , nos muestra anda 😲

Es el mismo problema que teníamos al principio. Kali no esta pudiendo asociar la IP de la maquina victima con  **admin.logan.hmv**.

Volvemos a editar el fichero **/etc/hosts** y modificamos la línea de la siguiente manera.

`10.0.3.10       logan.hmv admin.logan.hmv`

```bash
sudo nano /etc/hosts
```

![image.png](./imagenes/image%2016.png)

Refrescamos la pagina y vemos que ya funciona.

![image.png](./imagenes/image%2017.png)