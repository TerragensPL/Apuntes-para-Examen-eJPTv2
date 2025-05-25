  

### ¿Cómo puedo responder a la pregunta de cuantos HotFixes hay dentro de una maquina Windows?

  

> [!important] Un **Hotfix** es una solución (un tipo de actualización)rápida que se aplica a un software para corregir errores críticos, vulnerabilidades de seguridad o problemas específicos que afectan su funcionamiento. A diferencia de los parches tradicionales, los hotfixes suelen desarrollarse y desplegarse con urgencia, sin pasar por los procesos habituales de pruebas extensivas.

  

Después de haber hecho intrusión en una maquina Windows.

- Para ver los HotFix en un CMD **Get-HotFIX**
    
    ![[Imagenes/image 50.png|image 50.png]]
    
    Esto nos daría un error ya que este comando **se ejecuta en PowerShell.**
    
    - Entramos en PowerShell desde el CMD.
        
        `powershell`
        
        ![[Imagenes/image 1 39.png|image 1 39.png]]
        
    
      
    
    - Volvemos a lanzar el comando **Get-HotFIX**
        
        `Get-HotFIX`
        
        ![[Imagenes/image 2 38.png|image 2 38.png]]
        
    
      
    
    - Nos puede pasar que en un sistema encontramos con decenas de HotFix imposibilitándonos saber la cantidad de ellos. para solucionarlo escribimos lo siguiente y nos dará el numero total de HotFix.
        
        `(Get-HotFix).Count`
        
        ![[Imagenes/image 3 37.png|image 3 37.png]]
        

  

- Otra forma de enumerar los HotFixes seria con el siguiente comando. Aunque puede ser que dependiendo de la versión de Windows el comando **wmic** ya no este disponible.
    
    `wmic qfe list brief /format:table`