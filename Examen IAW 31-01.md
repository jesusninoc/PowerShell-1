# Script PS
```Powershell
Function Get-StringHash([String] $String,$HashName = "MD5") 
{
$String = Read-Host "Introduce la acción que deseas realizar"
$StringBuilder = New-Object System.Text.StringBuilder 
[System.Security.Cryptography.HashAlgorithm]::Create($HashName).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($String))|%{ 
[Void]$StringBuilder.Append($_.ToString("x2")) 
} 
$StringBuilder.ToString() 
}

$hash = Get-StringHash

$accion = $hash

if($accion -eq "f7531e2d0ea27233ce00b5f01c5bf335"){
    $html = invoke-webrequest "http://localhost:82/p001/examen31.php?enviar=f7531e2d0ea27233ce00b5f01c5bf335"

    $TD = $html.ParsedHtml.getElementsByTagName("h1")[0]

    $content = $TD | foreach { $_.innerText }

    $content

    $content = $content+"MB" 

    foreach($printer in (get-printer).name){
        foreach($printjob in Get-PrintJob $printer){
            $size = $printjob.size
            if ($size -gt $content){
                Remove-PrintJob $printjob.printerName -id ($printjob).id
            }
        
        }

    }
    Write-Host "Se han borrado todos los documentos superiores a 10MB en la cola de impresión"

}
if($accion -eq "4717d53ebfdfea8477f780ec66151dcb"){
    $html = invoke-webrequest "http://localhost:82/p001/examen31.php?enviar=4717d53ebfdfea8477f780ec66151dcb"

    $TD = $html.ParsedHtml.getElementsByTagName("h1")[0]

    $content = $TD | foreach { $_.innerText }

    $content

    $ssh = "ssh david@"+$content
    $pass = "Andel1928"
    start-process powershell "ssh david@$content"
    #Start-Process powershell
    start-sleep -Seconds 1
    [System.Windows.Forms.SendKeys]::SendWait($ssh)
    [System.Windows.Forms.SendKeys]::SendWait("{ENTER}")
    start-sleep -Seconds 5
    [System.Windows.Forms.SendKeys]::SendWait($pass)
    [System.Windows.Forms.SendKeys]::SendWait("{ENTER}")

    

}
if($accion -eq "36e5371ad91c9d2d09e9d7c0e76055db"){
    $html = invoke-webrequest "http://localhost:82/p001/examen31.php?enviar=36e5371ad91c9d2d09e9d7c0e76055db"

    $TD = $html.ParsedHtml.getElementsByTagName("h1")[0]

    $content = $TD | foreach { $_.innerText }

    $content

    New-NetFirewallRule -DisplayName "Allow Inbound Port 2021" -Direction Inbound -LocalPort $content.split(",")[0] -Protocol $content.split(",")[1] -Action Allow -Enabled True 

    Write-Host "Se ha creado una nueva regla, permitiendo la conexión al puerto 2021"
}
```

# Código PHP
```PHP
<!DOCTYPE html>
<html>

<head>
	<meta content="text/html; charset=utf-8" http-equiv="Content-Type">
	<title>Examen 31</title>
</head>

<body>
	<?php
				
		$enviar = htmlspecialchars($_GET['enviar']);
		
		$var = ("./ini/datos.ini");
		$base = parse_ini_file($var);		
		$php = new PDO($base["baseDeDatos"],$base["usuario"],$base["password"]);
		
		if($enviar == "f7531e2d0ea27233ce00b5f01c5bf335")
		{
			$con = $php->prepare("SELECT * FROM examen31 WHERE action = 'f7531e2d0ea27233ce00b5f01c5bf335';");
			$con->execute();
			$registros = $con->fetchAll(PDO::FETCH_NUM);
            
			$resultado = $registros[0][1];			
			?>
            <h1><?php echo $resultado ?></h1><?php
		} else if ($enviar == "4717d53ebfdfea8477f780ec66151dcb") {
			$con = $php->prepare("SELECT * FROM examen31 WHERE action = '4717d53ebfdfea8477f780ec66151dcb';");
			$con->execute();
			$registros = $con->fetchAll(PDO::FETCH_NUM);
            
			$resultado = $registros[0][1];			
			?>
            <h1><?php echo $resultado ?></h1><?php
		} else if ($enviar == "36e5371ad91c9d2d09e9d7c0e76055db") {
            $con = $php->prepare("SELECT * FROM examen31 WHERE action = '36e5371ad91c9d2d09e9d7c0e76055db';");
			$con->execute();
			$registros = $con->fetchAll(PDO::FETCH_NUM);
            
			$resultado = $registros[0][1];			
			?>
            <h1><?php echo $resultado ?></h1><?php
        }
	?>
</body>

</html>
```
