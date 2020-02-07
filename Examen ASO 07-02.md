```Powershell
function Menu 
{ 
     param ( 
           [string]$Titulo = 'Menu' 
     ) 
     cls 
     Write-Host "================ Menu ================" 
      
     
     Write-Host "1. Crear estructura"
     Write-Host "2. Crear usuarios"
     Write-Host "3. Crear grupos"
     Write-Host "4. Deshabilitar usuarios"
     Write-Host "5. Ver procesos de equipo remoto"
     Write-Host "6. Ver cola de impresión de equipo remoto"
     
     Write-Host "9. Borrar estructura"

     Write-Host "X. Salir"
}

do 
{ 
     Menu
     $input = Read-Host "¿Qué desea hacer?"
     switch ($input) 
     {

'1' {
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 1) {
            $ou = $ele.split(",")[1]
            New-ADOrganizationalUnit -Name $ou -ProtectedFromAccidentalDeletion $false -Path "DC=examen,DC=local"
            Write-Host "Se ha creado la unidad organizativa $ou"
        }
    }
    Write-Host "Se ha creado la estructura"

} '2' {
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 2){
            $ou = $ele.split(",")[3]
            $contra=$ele.split(",")[2]
            $SAMAccountName = $ele.split(",")[1]
            $nombre = $ele.split(",")[1]

            if(Get-ADUser -LDAPFilter "(samaccountname=$SAMAccountName)") {

                "No se puede crear, ya existe el usuario " + $SAMAccountName

            } else {

                New-ADUser -Name $nombre -Sam $nombre -Path "OU=$ou,DC=examen,DC=local" -AccountPassword (ConvertTo-SecureString $contra -AsPlainText -force) -Enable $true
                Write-Host "Se ha creado el usuario $nombre"

            }
        }
    }

} '3' {
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 3) {
            $grupo = $ele.split(",")[1]
            $ou=$ele.split(",")[2]
            New-ADGroup -Name $ele.split(",")[1] -Path "OU=$ou,DC=examen,DC=local" -Description "Grupo seguridad local" -GroupScope Global -GroupCategory Security
            Write-Host "Se ha creado el grupo $grupo"
            
            
        }
    }

} '4' { 

$output = gc .\examen.txt
    foreach($ele in $output){
        $user = $ele.split(",")[1]
        $ou = $ele.split(",")[2]
        if($ele.split(",")[0] -EQ 4) {
            Disable-ADAccount -Identity $user
            Move-ADObject -Identity "CN=$user,OU=$ou,DC=examen,DC=local" -TargetPath "OU=offline,DC=examen,DC=local"
            Write-Host "Se ha deshabilitado el usuario $user"
        }
    }


} '5' {

tasklist /s Windows7 /u Administrador
$count = (tasklist /s Windows7 /u Administrador).count

if($count -gt 10){
    [System.Windows.MessageBox]::Show('Hay más de 10 procesos ejecutandose ','Warning')
}



} '6' {

Get-PrintJob -ComputerName Windows7 -PrinterName "Brother"


} '9' {
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 1) {
            $ou = $ele.split(",")[1]
            Remove-ADOrganizationalUnit -Identity "OU=$ou,DC=examen,DC=local" -Recursive -Confirm:$false
            Write-Host "Se ha borrado la unidad organizativa $ou"
        }
    }
    Write-Host "Se ha borrado la estructura"
 
} 'X' { 
       cls 
       return
      }
     }

     pause 
} 
until ($input -eq 'X')
```

# Nota
La carpeta compartida se hace con New-PSDrive y sus respectivos parámetros, de ahí después se hace un gc (gc Z:\fichero.txt) y se cogen los datos.
No se ha podido hacer, porque Windows Server 2012 y Windows 7 no admiten ese cmdlet.
No se ha usado Windows 10 ni Windows Server 2019/2016 porque están demasiado cargados de funciones y características que el servidor no se puede usar con normalidad.
