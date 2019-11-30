```PowerShell
function mostrarMenu 
{ 
     param ( 
           [string]$Titulo = 'Menu' 
     ) 
     cls 
     Write-Host "================ $Titulo ================" 
      
     
     Write-Host "1. Crear usuarios"
     Write-Host "2. Crear grupos"
     Write-Host "3. Crear unidades organizativas"
     Write-Host "4. Saca información de procesos"
     Write-Host "5. Saca el hash de los modulos de un proceso"
     Write-Host "6. Crear usuario de una base de datos"
     Write-Host "7. Obtener procesos o servicios"
     Write-Host "8. Verificar la integridad del archivo de operaciones"
     Write-Host "E. Ejecuta un comando" 
     Write-Host "X. Salir" 
}

do 
{ 
     mostrarMenu
     $input = Read-Host "¿Qué desea hacer?"
     switch ($input) 
     {

'1' {
    
New-SSHSession -Computer 192.168.137.200 -Credential kike
$rawdatos = Invoke-SSHCommand -Index 0 "cat operaciones.txt"
$output = $rawdatos.Output
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 1){
        
            $contra=$ele.split(",")[2]
            $SAMAccountName = $ele.split(",")[1]

            if(Get-ADUser -LDAPFilter "(samaccountname=$SAMAccountName)") {

                "No se puede crear, ya existe el usuario " + $SAMAccountName
            } else {
            $nombre = $ele.split(",")[1]
            New-ADUser -Name $ele.split(",")[1] -Sam $ele.split(",")[1] -Path "OU=UnidadPrueba,DC=andel,DC=local" -AccountPassword (ConvertTo-SecureString $contra -AsPlainText -force) -Enable $true
            Write-Host "Se ha creado el usuario $nombre"
            }
        }

    }

} '2' {

New-SSHSession -Computer 192.168.137.200 -Credential kike
$rawdatos = Invoke-SSHCommand -Index 0 "cat operaciones.txt"
$output = $rawdatos.Output
foreach($ele in $output){

    if($ele.split(",")[0] -EQ 2) {
        $grupo = $ele.split(",")[1]
        New-ADGroup -Name $ele.split(",")[1] -Path "OU=UnidadPrueba,DC=andel,DC=local" -Description "Grupo seguridad local" -GroupScope Global -GroupCategory Security
        Write-Host "Se ha creado el grupo $grupo"
    }
}

} '3' {

New-SSHSession -Computer 192.168.137.200 -Credential kike
$rawdatos = Invoke-SSHCommand -Index 0 "cat operaciones.txt"
$output = $rawdatos.Output
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 3) {
            $ou = $ele.split(",")[1]
            New-ADOrganizationalUnit -Name $ele.split(",")[1] -ProtectedFromAccidentalDeletion $false -Path "DC=andel,DC=local"
            Write-Host "Se ha creado la unidad organizativa $ou"
        }
    }

} '4' {
New-SSHSession -Computer 192.168.137.200 -Credential kike
$rawdatos = Invoke-SSHCommand -Index 0 "cat operaciones.txt"
$output = $rawdatos.Output
    foreach($ele in $output){

        if($ele.split(",")[0] -EQ 4) {
            $pro = $ele.split(",")[1]
            $path = "C:\Users\Administrador\Desktop\ASO"
            gps $pro | Out-File C:\Users\Administrador\Desktop\ASO\info$pro.txt -Append
            Write-Host "Se ha creado el fichero info$pro.txt con la información de $pro en $path"
        }
    }

} '5' {
New-SSHSession -Computer 192.168.137.200 -Credential kike
$rawdatos = Invoke-SSHCommand -Index 0 "cat operaciones.txt"
$output = $rawdatos.Output
    foreach($ele in $output) {
        if($ele.split(",")[0] -EQ 5){

            foreach($module in gps $ele.split(",")[1] -Module) {
            $pro = $ele.split(",")[1]
            $pathh = "C:\Users\Administrador\Desktop\ASO"
            Get-FileHash $module.FileName | Out-File C:\Users\Administrador\Desktop\ASO\hashesde$pro.txt -Append
            Write-Host "Se está creando el fichero hashesde$pro.txt con la información del proceso $pro en $pathh"
            }
        }
    }

} '6' {
    $quien = Read-Host "Escribe el nombre del usuario: "


    [void][System.Reflection.Assembly]::LoadWithPartialName("MySql.Data")
    $Connection = New-Object MySql.Data.MySqlClient.MySqlConnection
    $ConnectionString = "server=" + "localhost" + ";port=3306;uid=" + "root" +";database="+"test"
    $Connection.ConnectionString = $ConnectionString
    $Connection.Open()

    $Query='select * from USUARIOS where nombre = "'+$quien+'"'
    $Command = New-Object MySql.Data.MySqlClient.MySqlCommand($Query, $Connection)
    $DataAdapter = New-Object MySql.Data.MySqlClient.MySqlDataAdapter($Command)
    $DataSet = New-Object System.Data.DataSet
    $RecordCount = $dataAdapter.Fill($dataSet, "data")
    $DataSet.Tables[0]
    $Connection.Close() 

    $nombre = $DataSet.Tables[0].nombre
    $pass = $DataSet.Tables[0].pass
    if(Get-ADUser -LDAPFilter "(samaccountname=$nombre)") {

                "No se puede crear, ya existe el usuario " + $nombre
    } else {
    New-ADUser -Name $nombre -Sam $nombre -Path "OU=UnidadPrueba,DC=andel,DC=local" -AccountPassword (ConvertTo-SecureString $pass -AsPlainText -force) -Enable $true
    Write-Host "Se ha creado el usuario $nombre"
    }

} '7' {

    $quien = Read-Host "¿Sobre qué quieres información?"


    [void][System.Reflection.Assembly]::LoadWithPartialName("MySql.Data")
    $Connection = New-Object MySql.Data.MySqlClient.MySqlConnection
    $ConnectionString = "server=" + "localhost" + ";port=3306;uid=" + "root" +";database="+"test"
    $Connection.ConnectionString = $ConnectionString
    $Connection.Open()

    $Query='select * from procesos where peticion = "'+$quien+'"'
    $Command = New-Object MySql.Data.MySqlClient.MySqlCommand($Query, $Connection)
    $DataAdapter = New-Object MySql.Data.MySqlClient.MySqlDataAdapter($Command)
    $DataSet = New-Object System.Data.DataSet
    $RecordCount = $dataAdapter.Fill($dataSet, "data")
    $DataSet.Tables[0]
    $Connection.Close() 

    $com = $DataSet.Tables[0].comando
    $pet = $DataSet.Tables[0].peticion
    $path = "C:\Users\Administrador\Desktop\ASO"
    $valores = $com | iex
    $valores | Out-File C:\Users\Administrador\Desktop\ASO\$pet.txt -Append
    if($quien -eq "procesos"){Write-Host "Se ha creado el fichero $pet.txt con el nombre de los procesos en $path"}
    if($quien -eq "servicios"){Write-Host "Se ha creado el fichero $pet.txt con el nombre de los servicios en $path"}

} '8' {

New-SSHSession -Computer 192.168.137.200 -Credential kike
$rawdatos = Invoke-SSHCommand -Index 0 "cat check.txt"
$output = $rawdatos.Output

$check = Invoke-SSHCommand -Index 0 "md5sum operaciones.txt"
$hash=$check.output
$buenhash=$hash.replace(" ",(","))
$superbuenhash=$buenhash.split(",")[0]
if($superbuenhash -eq $output){Write-Host "La integridad no se ha visto afectada"}else{Write-Host "La integridad se ha visto comprometida"}


} 'E' {

    $com = Read-Host "Escriba el comando que desee ejecutar"
    $com | iex


} 'X' { 
       cls 
       return
      }
     }
     pause 
} 
until ($input -eq 'X')

```
