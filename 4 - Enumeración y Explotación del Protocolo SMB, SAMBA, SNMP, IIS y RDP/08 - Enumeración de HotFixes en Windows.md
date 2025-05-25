# Enumeración de HotFixes en Windows

### ¿Cómo puedo responder a la pregunta de cuantos HotFixes hay dentro de una maquina Windows?

<aside>
💡

Un **Hotfix** es una solución (un tipo de actualización)rápida que se aplica a un software para corregir errores críticos, vulnerabilidades de seguridad o problemas específicos que afectan su funcionamiento. A diferencia de los parches tradicionales, los hotfixes suelen desarrollarse y desplegarse con urgencia, sin pasar por los procesos habituales de pruebas extensivas.

</aside>

Después de haber hecho intrusión en una maquina Windows.

- Para ver los HotFix en un CMD **Get-HotFIX**
    
    ![image.png](./imagenes/image%2086.png)
    
    Esto nos daría un error ya que este comando **se ejecuta en PowerShell.**
    
    - Entramos en PowerShell desde el CMD.
        
        `powershell`
        
        ![image.png](./imagenes/image%2087.png)
        
    
    - Volvemos a lanzar el comando **Get-HotFIX**
        
         `Get-HotFIX`
        
        ![image.png](./imagenes/image%2088.png)
        
    
    - Nos puede pasar que en un sistema encontramos con decenas de HotFix imposibilitándonos saber la cantidad de ellos. para solucionarlo escribimos lo siguiente y nos dará el numero total de HotFix.
        
        `(Get-HotFix).Count`
        
        ![image.png](./imagenes/image%2089.png)
        

- Otra forma de enumerar los HotFixes seria con el siguiente comando. Aunque puede ser que dependiendo de la versión de Windows el comando **wmic** ya no este disponible.
    
    `wmic qfe list brief /format:table`