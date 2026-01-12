### Install Docker-EE Windows:
```pwsh
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
```

### Install Docker-Compose Windows:
```pwsh
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
Invoke-WebRequest "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-Windows-x86_64.exe" `
-UseBasicParsing -OutFile $Env:ProgramFiles\Docker\docker-compose.exe
```

### Docker File Build:
```pwsh
C:<mainFolder>\docker build --pull --rm -f "<path to docker file>" -t <imageRepo/imageName:imageTag> "<imageContext in relation to mainFolder>"

docker build --pull --rm -f "IT_PowerShell\oneLogin\docker\DockerFile" -t it-registry.internal.kodiak.ai/new_account:latest "IT_PowerShell"
```

### Docker Image Push
```pwsh
docker push <imageRepo/imageName:imageTag>

docker push it-registry.internal.kodiak.ai/awxadinvsync:latest
```

### Start New User Container
```pwsh
docker run -it `
--env-file "C:\Users\e.voelker\Documents\Kodiak_Git\IT_PowerShell\oneLogin\.env_newAccount" `
-v C:\Users\e.voelker\Documents\Kodiak_Git\IT_PowerShell\oneLogin\NewAccountLogs:C:\Scripts\oneLogin\NewAccountLogs `
it-registry.internal.kodiak.ai/new_account:latest
```