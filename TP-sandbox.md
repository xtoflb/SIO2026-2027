# Configuration d'une sandbox
## 1. Configuration simple
<Configuration>
	<MappedFolder></MappedFolder>
	<Networking>Enable</Networking>
	<MemoryInMB>4096</MemoryInMB>
	<VGpu>Enable</VGpu>
	<LogonCommand>
		<Command>powerShell -Command "Set-ExecutionPolicy Bypass"</Command>
		<Command>powerShell -Command "Invoke-WebRequest 'https://github.com/ip7z/7zip/releases/download/26.04/7z2604-x64.exe' -outfile c:\users\WDAGUtilityAccount\Downloads\7z2604-x64.exe"</Command>
		<Command>PowerShell - Command "Start-Process -FilePath C:\Users\WDAGUtilityAccount\Downloads\7z2604-x64.exe -ArgumentList '/S'"</Command>
	</LogonCommand>
</Configuration>
