# Formulario para subir imagenes

```PHP
<form action="method2.php" method="get">
	<h1>Formulario</h1>
	<hr>
	<h2>Identifícate: </h2>
	Usuario: <input type="text" name="user" />
	Contraseña: <input type="text" name="pass" />
	<br>
	<h3>Introduce la ruta de la imagen que quieres subir</h3>
	<br>
    Ruta: <input type="text" name="img" />
	<br />
	<br>
    <input type="submit" name="submit" value="Enviar" />
</form>
```

# Sesión (method2.php)
```PHP
<?php

	$var = "C:/xampp/htdocs/p001/ini/datos.ini";
	$base = parse_ini_file($var);		
	$php = new PDO($base["baseDeDatos"],$base["usuario"],$base["password"]);
	
		$user = htmlspecialchars($_GET['user']);
		$pass = htmlspecialchars($_GET['pass']);
		$hashpass = md5($pass);
		$img = htmlspecialchars($_GET['img']);
		unset($goodpass);
		
		$command1 = "[System.Convert]::ToBase64String((Get-Content ";
		$command2 = " -Encoding Byte)) > codedimg.txt";
		$command = $command1 . $img . $command2;
		$codedcomm = (string)base64_encode($command);
		$decodedcomm = base64_decode($codedcomm);
		
		$con = $php->prepare("SELECT password FROM usuarios WHERE login = '$user';");
		$con->execute();
		$con->bindParam(':tex',$user);
		$registros = $con->fetchAll(PDO::FETCH_NUM);
            
		$goodpass = $registros[0][0];

	session_start();
	if($_SERVER["REQUEST_METHOD"] == "GET")
	{
    if($hashpass == $goodpass)
    {
		
		Echo "<html>";
        Echo "<title>MUY bien</title>";
        Echo "<b>Acceso concedido</b>";
		echo "<br>";
		echo "<h1>Imagen subida</h1>";
		$ps = system("powershell $decodedcomm");
		$ps2 = system("powershell -encodedcommand ZwBjACAALgAvAGMAbwBkAGUAZABpAG0AZwAuAHQAeAB0AA==");
		
		
		
		$con = $php->prepare("INSERT INTO examen VALUES (:tex, :tex2, :tex3);");
		$con->bindParam(':tex',$ps2);
		$con->bindParam(':tex2',$img);
		$con->bindParam(':tex3',$user);
		$con->execute();
		
		
		
        session_destroy();    
 
        return true;
		
		
		
       
    }
    else {
    
		Echo "<html>";
        Echo "<title>MUY mal</title>";
        Echo "<h1>Acceso denegado</h1>";        
        
        session_destroy() ;
        
        return false;
        
    }
}

?>
```
