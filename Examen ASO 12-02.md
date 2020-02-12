```Powershell
cd C:\Users\Administrador\Desktop
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
     Write-Host "5. Crear carpetas usuarios"
     Write-Host "6. Controlar impresión"
     Write-Host "7. Crear copias de carpetas de usuarios"
     
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
cd C:\Users\Administrador\Desktop
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 1) {
            $ou = $ele.split(",")[1]
            New-ADOrganizationalUnit -Name $ou -ProtectedFromAccidentalDeletion $false -Path "DC=kike,DC=local"
            Write-Host "Se ha creado la unidad organizativa $ou"
        }
    }
    Write-Host "Se ha creado la estructura"

} '2' {
cd C:\Users\Administrador\Desktop
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

                New-ADUser -Name $nombre -Sam $nombre -Path "OU=$ou,DC=kike,DC=local" -AccountPassword (ConvertTo-SecureString $contra -AsPlainText -force) -Enable $true
                Write-Host "Se ha creado el usuario $nombre"

            }
        }
    }

} '3' {
cd C:\Users\Administrador\Desktop
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 3) {
            $grupo = $ele.split(",")[1]
            $ou=$ele.split(",")[2]
            New-ADGroup -Name $ele.split(",")[1] -Path "OU=$ou,DC=kike,DC=local" -Description "Grupo seguridad local" -GroupScope Global -GroupCategory Security
            Write-Host "Se ha creado el grupo $grupo"
            
            
        }
    }

} '4' { 
cd C:\Users\Administrador\Desktop
$output = gc .\examen.txt
    foreach($ele in $output){
        $user = $ele.split(",")[1]
        $ou = $ele.split(",")[2]
        if($ele.split(",")[0] -EQ 4) {
            Disable-ADAccount -Identity $user
            Move-ADObject -Identity "CN=$user,OU=$ou,DC=kike,DC=local" -TargetPath "OU=offline,DC=kike,DC=local"
            Write-Host "Se ha deshabilitado el usuario $user"
        }
    }


} '5' {
cd C:\Users\Administrador\Desktop
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 2){
            $SAMAccountName = $ele.split(",")[1]
            $nombre = $ele.split(",")[1]

            if(Get-ADUser -LDAPFilter "(samaccountname=$SAMAccountName)") {

                cd C:\Compartidas
                mkdir $nombre
                New-SmbShare -Name Carpeta$nombre -Path C:\Compartidas\$nombre –FullAccess kike\$nombre
                Write-Host "Se ha creado una carpeta para $nombre"
            }
        }
    }


} '6' {
cd C:\Users\Administrador\Desktop
$user = Read-Host "De que usuario desea controlar la impresion"

$howmany = ((Get-PrintJob "Brother" | select username).username | Select-String $user).count
foreach($print in Get-PrintJob "Brother" )
{
    if($print.username -eq $user)
    {
        Remove-PrintJob -id
    }
}



} '7' {
cd C:\Users\Administrador\Desktop
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 2){
            $SAMAccountName = $ele.split(",")[1]
            $nombre = $ele.split(",")[1]
            cd C:\Compartidas
            Copy-Item .\$nombre C:\Compartidas\Backups\Backup$nombre -Recurse
            Write-Host "Se ha creado una copia de la carpeta de $nombre"
        }
    }




} '9' {
cd C:\Users\Administrador\Desktop
$output = gc .\examen.txt
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 1) {
            $ou = $ele.split(",")[1]
            Remove-ADOrganizationalUnit -Identity "OU=$ou,DC=kike,DC=local" -Recursive -Confirm:$false
            Write-Host "Se ha borrado la unidad organizativa $ou"
        }

        if($ele.split(",")[0] -EQ 2){
            $SAMAccountName = $ele.split(",")[1]
            $nombre = $ele.split(",")[1]

           cd C:\Compartidas
           Remove-SmbShare -Name Carpeta$nombre -Confirm:$false
           rm "u*" -Force
           cd C:\Compartidas\Backups
           rm "u*" -Force
           
            
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
