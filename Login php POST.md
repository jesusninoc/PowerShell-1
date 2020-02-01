# Form
```PHP
<form action="login.php" method="post">
    Usuario: <input type="text" name="user" /><br />
	Pass: <input type="text" name="pass" /><br />
	
    <input type="submit" name="submit" value="Enviar" />
</form>
```

# Action
```PHP

<?PHP
session_start();
if($_SERVER["REQUEST_METHOD"] == "POST")
{		
		$user=$_POST['user'];
		$pass=$_POST['pass'];
		$var = ("C:/xampp/htdocs/p001/ini/datos.ini");
		$base = parse_ini_file($var);		
		$php = new PDO($base["baseDeDatos"],$base["usuario"],$base["password"]);
		
			$con = $php->prepare("SELECT * FROM usuarios where login = :tex;");
			$con->bindParam(':tex',$user);
			$con->execute();
			$registros = $con->fetchAll(PDO::FETCH_NUM);
            //unset($usuario);
			//unset($contra);
			$usuario = $registros[0][0];
			$contra = $registros[0][1];
			$hash = hash("sha1", $pass);
			

    if($user == $usuario && $hash == $contra)
    {		
		Echo "<html>";
        Echo "<title>MUY bien</title>";
        Echo "<h1>Acceso concedido</h1>";
		echo "<p>$user</p>";
		echo "<p>$pass</p>";
 
        session_destroy();    
 
        return true;
		}
		else
		{
		$espacio = " ";
		Echo "<html>";
        Echo "<title>MUY mal</title>";
        Echo "<h1>Acceso denegado</h1>";
		echo "<p>Se ha creado un registro con el intento fallido</p>";
		$file = fopen("log.txt", "w");
		fwrite($file, $user . $espacio . $pass . PHP_EOL);
		fclose($file);

		$file = fopen("log.txt", "r");
	

		fclose($file);
		}
}
?>

```
