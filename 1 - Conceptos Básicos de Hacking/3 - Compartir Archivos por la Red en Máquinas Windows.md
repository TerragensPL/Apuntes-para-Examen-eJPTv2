# Compartir Archivos por la Red en Máquinas Windows

- En nuestra maquina Kali con Python levantamos un servidor web  en el puerto 80.
    
    ```bash
    python3 -m http.server 80
    ```
    
    ![image.png](image%207.png)
    

- En la maquina Windows abrimos un terminar CMD y escribimos el siguiente comando:
    
    certutil -split -urlcache -f [http://192.168.1.37/imagen.png](http://192.168.1.37/imagen.png) imagen.png
    
    Se utiliza para descargar un archivo desde una URL específica y almacenarlo en la caché local de Windows. 
    
    **Parámetros explicados**
    
    **-split**: Este parámetro indica que, **si el archivo descargado es grande, se dividirá en partes** más pequeñas para facilitar su manejo.
    
    **-urlcache**: Este parámetro especifica que **el archivo se descargará desde una URL y se almacenará en la caché local**.
    
    **-f:** **Obliga a sobrescribir cualquier archivo existente con el mismo nombre** en la caché local, sin pedir confirmación.
    
    **http://10.0.3.4/imagen.png**: Es la **URL del archivo que se desea descargar**. En este caso, apunta a un archivo llamado imagen.png alojado en un servidor con la dirección IP 192.168.1.37 .
    
    **imagen.png**: Es el n**ombre del archivo bajo el cual se guardará en tu sistema**. Si no lo incluyes, el archivo mantendrá el nombre original del recurso remoto.
    
    ![image.png](image%208.png)