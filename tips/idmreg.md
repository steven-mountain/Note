```powershell
# Enable TLSv1.2 for compatibility with older clients
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor [System.Net.SecurityProtocolType]::Tls12

$DownloadURL = 'https://github.com/steven-mountain/Note/blob/901462d68299566678acaac07ee9e1fe8e383a54/tips/IAS_0.7_ModByPiash.cmd'

$FilePath = "$env:TEMP\IAS.cmd"

try {
    Invoke-WebRequest -Uri $DownloadURL -UseBasicParsing -OutFile $FilePath
} catch {
    Write-Error $_
	Return
}

if (Test-Path $FilePath) {
    Start-Process $FilePath -Wait
    $item = Get-Item -LiteralPath $FilePath
    $item.Delete()
}

```

