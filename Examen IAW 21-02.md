# Form
```PHP
<form action="method.php" method="get">
	<h1>Introduce la ruta de la imagen que quieres subir</h1>
	<br>
    Ruta: <input type="text" name="nombre" />
	<br />
    <input type="submit" name="submit" value="Enviar" />
</form>
```

# Method
```PHP
<?php
	
		$nombre = htmlspecialchars($_GET['nombre']);
		$coded = base64_encode($nombre);
		
		$command1 = "[System.Convert]::ToBase64String((Get-Content ";
		$command2 = " -Encoding Byte)) > codedimg.txt";
		$command = $command1 . $nombre . $command2;
		$codedcomm = (string)base64_encode($command);
		$decodedcomm = base64_decode($codedcomm);
		
		
		$ps = system("powershell $decodedcomm");
		$ps2 = system("powershell -encodedcommand ZwBjACAALgAvAGMAbwBkAGUAZABpAG0AZwAuAHQAeAB0AA==");
		
		$var = "C:/xampp/htdocs/p001/ini/datos.ini";
		$base = parse_ini_file($var);		
		$php = new PDO($base["baseDeDatos"],$base["usuario"],$base["password"]);
		
		$con = $php->prepare("INSERT INTO examen21 VALUES (:tex, :tex2);");
		$con->bindParam(':tex',$ps2);
		$con->bindParam(':tex2',$nombre);
		$con->execute();
	?>
	<h1><?php echo "Imagen subida" ?></h1>
```

# Pickimg (form)
```PHP
<form action="pick.php" method="get">
	<h1>Introduce la imagen que quieres ver</h1>
	<br>
    Ruta: <input type="text" name="nombre" />
	<br />
    <input type="submit" name="submit" value="Enviar" />
</form>
```

# Pick (method)
```PHP
<?php
		
		$var = ("C:/xampp/htdocs/p001/ini/datos.ini");
		$base = parse_ini_file($var);		
		$php = new PDO($base["baseDeDatos"],$base["usuario"],$base["password"]);
	$what = htmlspecialchars($_GET['nombre']);

$con = $php->prepare("SELECT coded FROM examen21 WHERE raw = '$what';");
$con->execute();
$con->bindParam(':tex',$what);
$registros = $con->fetchAll(PDO::FETCH_NUM);
            
$resultado = $registros[0][0];
		
?>
<img alt="La imagen no se puede mostrar" src="data:;base64,<?php echo "$resultado"; ?>" width="100" height="100"" />
```

