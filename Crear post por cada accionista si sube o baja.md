```PowerShell

$web=Invoke-WebRequest ‘http://www.bolsamadrid.es/esp/aspx/Mercados/Precios.aspx?indice=ESI100000000’ 
$bancobaja=$web.AllElements | Where Class -eq “DifFlBj” | %{$_.innerText} 
$bancosube=$web.AllElements | Where Class -eq “DifFlSb” | %{$_.innerText} 
$bancoigual=$web.AllElements | Where Class -eq “DifFlIg” | %{$_.innerText}
$bancobaja > C:\Users\Administrador\Desktop\IAW\bancobaja.txt
$bancosube > C:\Users\Administrador\Desktop\IAW\bancosube.txt
$bancoigual > C:\Users\Administrador\Desktop\IAW\bancoigual.txt

$bancosdown = gc C:\Users\Administrador\Desktop\IAW\bancobaja.txt
$bancosup = gc C:\Users\Administrador\Desktop\IAW\bancosube.txt
$bancostay = gc C:\Users\Administrador\Desktop\IAW\bancoigual.txt


$valorbaja=$web.AllElements | Where Class -eq “DifClBj” | %{$_.innerText} 
$valorsube=$web.AllElements | Where Class -eq “DifClSb” | %{$_.innerText} 
$valorigual=$web.AllElements | Where Class -eq “DifClIg” | %{$_.innerText}
$valorbaja > C:\Users\Administrador\Desktop\IAW\valorbaja.txt
$valorsube > C:\Users\Administrador\Desktop\IAW\valorsube.txt
$valorigual > C:\Users\Administrador\Desktop\IAW\valorigual.txt

$valoresdown = gc C:\Users\Administrador\Desktop\IAW\valorbaja.txt
$valoresup = gc C:\Users\Administrador\Desktop\IAW\valorsube.txt
$valorestay = gc C:\Users\Administrador\Desktop\IAW\valorigual.txt

cd C:\xampp\htdocs\bancos
mkdir C:\xampp\htdocs\bancos\subeobaja -Force

$vhost =
    "<VirtualHost *:82>
    ServerAdmin admin@subeobaja.com
    DocumentRoot C:\xampp\htdocs\bancos\subeobaja
    ServerName subeobaja.com
    ServerAlias www.subeobaja.com
</VirtualHost>
    "

add-Content C:\xampp\apache\conf\extra\httpd-vhosts.conf -Value $vhost

$localhost = "127.0.0.1 www.subeobaja.com"

add-Content C:\Windows\System32\drivers\etc\hosts -Value $localhost

cd C:\xampp\htdocs\bancos\subeobaja
php C:\xampp\htdocs\wptest\wp-cli.phar core download --locale=es_ES
php C:\xampp\htdocs\wptest\wp-cli.phar config create --dbname=subeobaja --dbuser=root --dbhost=127.0.0.1
php C:\xampp\htdocs\wptest\wp-cli.phar db create
php C:\xampp\htdocs\wptest\wp-cli.phar core install --url=http://www.subeobaja.com:82 --title="Sube O Baja!" --admin_user=admin --admin_password=Andel1928 --admin_email=~admin@suben.com
#php C:\xampp\htdocs\wptest\wp-cli.phar plugin install wp-super-cache --activate
#php C:\xampp\htdocs\wptest\wp-cli.phar plugin install wordpress-seo --activate
#php C:\xampp\htdocs\wptest\wp-cli.phar plugin install wptouch --activate

$linea = 2
foreach($banco in $bancosup){

    $banco=$banco.replace(".","")
    $banco=$banco.replace("®","")
    
    
    $valor = $valoresup | Select -Index ($linea - 1)
    
    php C:\xampp\htdocs\wptest\wp-cli.phar post create --post_title="$banco sube!" --post_content="Las acciones de $banco suben un $valor %" --post_type="post" --post_status="publish"
    $linea++
}

$lineo = 1
foreach($banca in $bancosdown){

    $banca=$banca.replace(".","")
    $banca=$banca.replace("®","")
    
    
    $valo = $valoresdown | Select -Index ($lineo - 1)
    
    php C:\xampp\htdocs\wptest\wp-cli.phar post create --post_title="$banca baja!" --post_content="Las acciones de $banca bajan un $valo %" --post_type="post" --post_status="publish"
    $lineo++
}

```
