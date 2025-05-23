  

Cuando se hace la intrusión en una maquina hay que comprobar siempre las interfaces de red.

- Con `Ifconfig` o `hostname -I` en equipos Linux y fijarse si hay mas de una interface de red .
    
    ![[Imagenes/image 61.png|image 61.png]]
    
    ![[Imagenes/image 1 50.png|image 1 50.png]]
    

  

- En Windows con `ipconfig`.
    
    ![[Imagenes/image 2 49.png|image 2 49.png]]
    

  

En caso de encontrar una segunda interface de red podríamos hacer **pivoting.**

Básicamente, es usar una máquina comprometida como un **punto de salto** para acceder a otros sistemas o redes que no eran accesibles directamente desde su posición original.

  

![[Imagenes/image 3 48.png|image 3 48.png]]