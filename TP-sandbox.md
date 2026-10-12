# Configuration d'une sandbox
## 1. Configuration simple
```wsb
<Configuration>
	<MappedFolder></MappedFolder>
	<Networking>Enable</Networking>
	<MemoryInMB>4096</MemoryInMB>
	<VGpu>Enable</VGpu>
	<LogonCommand>
		<Command>powerShell -Command "-ExecutionPolicy Bypass"</Command>
		<Command>powerShell -Command "Invoke-WebRequest 'https://github.com/ip7z/7zip/releases/download/26.04/7z2604-x64.exe' -outfile c:\users\WDAGUtilityAccount\Downloads\7z2604-x64.exe"</Command>
		<Command>PowerShell - Command "Start-Process -FilePath C:\Users\WDAGUtilityAccount\Downloads\7z2604-x64.exe -ArgumentList '/S'"</Command>
	</LogonCommand>
</Configuration>
```
## 2. Configuration pour test avec wannacry
```wsb
<Configuration>
	<MappedFolder></MappedFolder>
	<Networking>Enable</Networking>
	<MemoryInMB>4096</MemoryInMB>
	<VGpu>Enable</VGpu>
	<LogonCommand>
		<!-- Désactiver la protection temps réel -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Set-MpPreference -DisableRealtimeMonitoring $true"</Command>

		<!-- Télécharger 7-zip -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest 'https://www.7-zip.org/a/7z2501-x64.exe' -OutFile C:\Users\WDAGUtilityAccount\Downloads\7z2501-x64.exe"</Command>

    	<!-- Télécharger regshot -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest 'https://github.com/Seabreg/Regshot/blob/master/Regshot-x64-ANSI.exe' -OutFile C:\Users\WDAGUtilityAccount\Downloads\Regshot-x64-ANSI.exe"</Command>

    	<!-- Télécharger ProcessExplorer -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest 'https://download.sysinternals.com/files/ProcessExplorer.zip' -OutFile C:\Users\WDAGUtilityAccount\Downloads\ProcessExplorer.zip"</Command>

    	<!-- Télécharger ProcessMonitor -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest 'https://download.sysinternals.com/files/ProcessMonitor.zip' -OutFile C:\Users\WDAGUtilityAccount\Downloads\ProcessMonitor.zip"</Command>

    	<!-- Télécharger TCPView -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest 'https://download.sysinternals.com/files/TCPView.zip' -OutFile C:\Users\WDAGUtilityAccount\Downloads\TCPView.zip"</Command>

    	<!-- Télécharger Notepad++ -->
    	<Command>powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest 'https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.8.6/npp.8.8.6.Installer.x64.exe' -OutFile C:\Users\WDAGUtilityAccount\Downloads\npp.8.8.6.Installer.x64.exe"</Command>
	</LogonCommand>
</Configuration>
```
