# escape=`

FROM mcr.microsoft.com/windows/servercore:1803

# Install .NET 4.8
RUN powershell -Command `
        $ProgressPreference = 'SilentlyContinue'; `
        Invoke-WebRequest -Uri "https://download.visualstudio.microsoft.com/download/pr/7afca223-55d2-470a-8edc-6a1739ae3252/abd170b4b0ec15ad0222a809b761a036/ndp48-x86-x64-allos-enu.exe" -OutFile dotnet-framework-installer.exe & `
    .\dotnet-framework-installer.exe /q & `
    del .\dotnet-framework-installer.exe & `
    powershell Remove-Item -Force -Recurse ${Env:TEMP}\*

# Apply latest patch
RUN powershell -Command `
        $ErrorActionPreference = 'Stop'; `
        $ProgressPreference = 'SilentlyContinue'; `
        Invoke-WebRequest `
            -UseBasicParsing `
            -Uri "http://download.windowsupdate.com/c/msdownload/update/software/secu/2019/05/windows10.0-kb4499167-x64_23697fcf76f2d755b492415eb89aa573c8804765.msu" `
            -OutFile patch.msu; `
        New-Item -Type Directory patch; `
        Start-Process expand -ArgumentList 'patch.msu', 'patch', '-F:*' -NoNewWindow -Wait; `
        Remove-Item -Force patch.msu; `
        Add-WindowsPackage -Online -PackagePath C:\patch\Windows10.0-kb4499167-x64.cab; `
        Remove-Item -Force -Recurse \patch

# ngen .NET Fx
ENV COMPLUS_NGenProtectedProcess_FeatureEnabled 0
RUN \Windows\Microsoft.NET\Framework64\v4.0.30319\ngen update & `
    \Windows\Microsoft.NET\Framework\v4.0.30319\ngen update
