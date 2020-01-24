```Powershell
$accion = Read-host = "Introduce la accion"
if($accion -eq "print"){
    $html = invoke-webrequest "http://localhost:82/p001/examen24.php?enviar=print"

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
    Write-Host "Se han borrado todos los documentos superiores a 10KB en la cola de impresión"

}
if($accion -eq "connection"){
    $html = invoke-webrequest "http://localhost:82/p001/examen24.php?enviar=connection"

    $TD = $html.ParsedHtml.getElementsByTagName("h1")[0]

    $content = $TD | foreach { $_.innerText }

    $content

    $ssh = "ssh kike@"+$content
    $pass = "Andel1928"
    #start-process powershell "ssh kike@$content"
    Start-Process powershell
    start-sleep -Seconds 1
    [System.Windows.Forms.SendKeys]::SendWait($ssh)
    [System.Windows.Forms.SendKeys]::SendWait("{ENTER}")
    [System.Windows.Forms.SendKeys]::SendWait($pass)
    [System.Windows.Forms.SendKeys]::SendWait("{ENTER}")

    

}
if($accion -eq "firewall"){
    $html = invoke-webrequest "http://localhost:82/p001/examen24.php?enviar=firewall"

    $TD = $html.ParsedHtml.getElementsByTagName("h1")[0]

    $content = $TD | foreach { $_.innerText }

    $content

    New-NetFirewallRule -DisplayName "Allow Inbound Port 2021" -Direction Inbound -LocalPort $content.split(",")[0] -Protocol $content.split(",")[1] -Action Allow -Enabled True 

    Write-Host "Se ha creado una nueva regla, permitiendo la conexión al puerto 2021"
}
´´´

```PHP
<!DOCTYPE html>
<html>

<head>
	<meta content="text/html; charset=utf-8" http-equiv="Content-Type">
	<title>P001</title>
</head>

<body>
	<?php
				
		$enviar = htmlspecialchars($_GET['enviar']);
		
		$var = ("./ini/datos.ini");
		$base = parse_ini_file($var);		
		$php = new PDO($base["baseDeDatos"],$base["usuario"],$base["password"]);
		
		if($enviar == "print")
		{
			$con = $php->prepare("SELECT * FROM examen24 WHERE accion = 'print';");
			$con->execute();
			$registros = $con->fetchAll(PDO::FETCH_NUM);
            
			$resultado = $registros[0][1];			
			?>
            <h1><?php echo $resultado ?></h1><?php
		} else if ($enviar == "connection") {
			$con = $php->prepare("SELECT * FROM examen24 WHERE accion = 'connection';");
			$con->execute();
			$registros = $con->fetchAll(PDO::FETCH_NUM);
            
			$resultado = $registros[0][1];			
			?>
            <h1><?php echo $resultado ?></h1><?php
		} else if ($enviar == "firewall") {
            $con = $php->prepare("SELECT * FROM examen24 WHERE accion = 'firewall';");
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
