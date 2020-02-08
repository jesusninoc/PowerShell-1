```Powershell

(New-Object -com SAPI.SpVoice).speak("Voy a sacar una foto a un payaso")

start microsoft.windows.camera:

Start-Sleep -Seconds 2

$MouseEventSig=@'
[DllImport("user32.dll",CharSet=CharSet.Auto, CallingConvention=CallingConvention.StdCall)]
public static extern void mouse_event(long dwFlags, long dx, long dy, long cButtons, long dwExtraInfo);
'@
 
$MouseEvent = Add-Type -memberDefinition $MouseEventSig -name "MouseEventWinApi" -passThru

[System.Windows.Forms.Cursor]::Position = New-Object System.Drawing.Point(1521, 536)
$MouseEvent::mouse_event(0x00000002, 0, 0, 0, 0)
$MouseEvent::mouse_event(0x00000004, 0, 0, 0, 0)
Start-Sleep -Seconds 1

Stop-Process -ProcessName "WindowsCamera"

$dir = "C:\Users\Kike\Pictures\Camera Roll"
$latest = Get-ChildItem -Path $dir | Sort-Object LastAccessTime -Descending | Select-Object -First 1
$pic = $dir+"\"+
$latest.name
ii $pic

(New-Object -com SAPI.SpVoice).speak("jajajajajajajajajajajajajajajajajajajajajajajajajajajajajajajaja")
```
