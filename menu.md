```PowerShell
function Menu 
{ 
     param ( 
           [string]$Titulo = 'Menu' 
     ) 
     cls 
     Write-Host "================ $Titulo ================" 
      
     
     Write-Host "1. Función"
     Write-Host "X. Salir" 
}

do 
{ 
     Menu
     $input = Read-Host "¿Qué desea hacer?"
     switch ($input) 
     {

'1' {

#---------------Aquí va la funcion-------------

} 'X' { 
       cls 
       return
      }
     }
     pause 
} 
until ($input -eq 'X')
```
