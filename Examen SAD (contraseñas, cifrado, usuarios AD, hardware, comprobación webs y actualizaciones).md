```PowerShell
function Menu 
{ 
     param ( 
           [string]$Titulo = 'Menu' 
     ) 
     cls 
     Write-Host "================ $Titulo ================" 
      
     
     Write-Host "1. Cifrar"
     Write-Host "2. Descifrar"
     Write-Host "3. Crear un usuario en el AD con una contraseña segura"
     Write-Host "4. Obtener información sobre el hardware y el software del equipo"
     Write-Host "5. Comprobar si una página web existe"
     Write-Host "6. Obtener información sobre las últimas actualizaciones instaladas"
     Write-Host "X. Salir" 
}

do 
{ 
     Menu
     $input = Read-Host "¿Qué desea hacer?"
     switch ($input) 
     {

'1' {

#############
## CIFRAR ###
#############

$cadena = Read-Host "Introduzca texto, posteriormente se cifrará"
$ruta = Read-Host "Introduzca la ruta donde se guardará el fichero con el contenido cifrado"
$clave = Read-Host "Introduzca la clave de cifrado"
 
#La clave para cifrar tiene que ser correcta en tipo System.Byte
#ConvertFrom-SecureString : No se puede enlazar el parámetro 'Key'. No se puede convertir el valor "Some secret key" al tipo "System.Byte". Error: "La cadena de entrada no tiene el formato correcto."
 
$clavebyte=[Byte[]]($clave.PadRight(24).Substring(0,24).ToCharArray())
 
$cadenacifrada=$cadena | ConvertTo-SecureString -AsPlainText -Force | ConvertFrom-SecureString -Key $clavebyte
$cadenacifrada | Out-File $ruta -Force
write-host "Se ha creado el fichero con el contenido cifrado en" $ruta

} '2' {

################
## DESCIFRAR ###
################
$rutaci = Read-Host "Introduzca la ruta del fichero que desea descifrar"
$rutades = Read-Host "Introduzca la ruta del fichero donde desea guardar el contenido descifrado"
$cadenacifrada = gc $rutaci
 
$clave=Read-Host "Introduzca la clave de cifrado"
 
#La clave para cifrar tiene que ser correcta en tipo System.Byte
$clavebyte=[Byte[]]($clave.PadRight(24).Substring(0,24).ToCharArray())
 
$cadenadescifrando = $cadenacifrada | ConvertTo-SecureString -Key $clavebyte
 
$credencial = New-Object -TypeName System.Management.Automation.PSCredential('clave123', $cadenadescifrando)
$cadenadescrifrada = $credencial.GetNetworkCredential().Password
$cadenadescrifrada | Out-File $rutades
write-host "Se ha creado un fichero con el contenido descifrado en" $rutades

} '3' {

$nombre=Read-Host "Introduzca un nombre de usuario"

if(Get-ADUser -LDAPFilter "(samaccountname=$nombre)") {

    "No se puede crear, ya existe el usuario " + $nombre

} else {

    $pass = Read-Host "Introduce una contraseña"

#-----------Comprueba en diccionario---------------------------------------------
#cd C:\Users\Administrador\Desktop\SAD
#foreach($ele in gc C:\Users\Administrador\Desktop\SAD\rockyou.txt){
#    while($ele -eq $pass){
#        $pass=Read-Host "Tu contraseña no es segura, introduce otra diferente"
#    }
#}

#--------Comprueba si la contraseña tiene 8 caracteres---------------

while($pass.Length -lt 8){
    $pass=Read-Host "Tu contraseña no es cumple los requisitos de longitud"
}

#--------Comprueba si la contraseña tiene al menos una mayúscula---------------

while($pass -cmatch '[A-Z]' -eq $false){
    $pass=Read-Host "Tu contraseña debe contener al menos una mayúscula"
}

#--------Comprueba si la contraseña tiene al menos un número---------------

while($pass -cmatch '[0-9]' -eq $false){
    $pass=Read-Host "Tu contraseña debe contener al menos un número"
}

#--------Comprueba si la contraseña tiene al menos carácter especial---------------

while($pass -match '[^a-zA-Z0-9]' -eq $false){
    $pass=Read-Host "Tu contraseña debe contener al menos un carácter especial"
}
    
    New-ADUser -Name $nombre -Sam $nombre -Path "OU=UnidadPrueba,DC=andel,DC=local" -AccountPassword (ConvertTo-SecureString $pass -AsPlainText -force) -Enable $true
    Write-Host "Se ha creado el usuario:" $nombre
}





} '4' {

#Llamadas WMI
$ComputerSystem=Get-WmiObject Win32_ComputerSystem
$BaseBoard=Get-WmiObject Win32_BaseBoard
$BIOS=Get-WmiObject Win32_BIOS
$Processor=Get-WmiObject Win32_Processor
$Battery=Get-WmiObject Win32_Battery
 
#Crear un objeto con todos los datos sobre el hardware
$obj=[PSCustomObject]@{
 Model = $ComputerSystem.Model
 ManufacturerBoard = $BaseBoard.Manufacturer
 BIOSVersion = $BIOS.SMbiosbiosversion
 BIOSSerialNumber = $BIOS.serialnumber
 ManufacturerProcessor=$Processor.Manufacturer
 MaxClockSpeed=$Processor.MaxClockSpeed
 DeviceIDBattery=$Battery.DeviceID.trim()
} | Out-File C:\Users\Administrador\Desktop\SAD\infoequipo.txt -Force

$soft = Get-WmiObject win32_product | select Name

$soft | Out-File C:\Users\Administrador\Desktop\SAD\infoequipo.txt -Append

write-host "Se ha creado un fichero con la información hardware y software del equipo"

} '5' {

    $web=Read-Host "Introduce una página web:"

    $url = iwr $web
    $statuscode=$url.statuscode
    if($statuscode -eq 200){
        Write-Host "La página web existe y está activa"
    } else {
        Write-Host "Esta página web no existe o tiene problemas"
    }

} '6' {

$Sesion = New-Object -ComObject Microsoft.Update.Session
$actualizacion = $Sesion.CreateUpdateSearcher().Search("IsInstalled=1").Updates
$actualizacion | Select-Object Title, LastDeploymentChangeTime | Out-File C:\Users\Administrador\Desktop\SAD\infoupdates.txt -Force
Write-Host "Se ha creado un fichero con información sobre las últimas actualizaciones instaladas"

} 'X' { 
       cls 
       return
      }
     }
     pause 
} 
until ($input -eq 'X')
```
