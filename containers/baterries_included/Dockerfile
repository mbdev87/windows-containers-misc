# escape=`

# Windows servercore image with msbuild, dotnet, python, perforce and git for various CI workloads
# with everything added to the %PATH%

# Use the latest Windows Server Core 2022 image.
FROM mcr.microsoft.com/windows/servercore:ltsc2022
SHELL ["cmd", "/S", "/C"]

# Install buildtools
RUN `
    # Download the Build Tools bootstrapper.
    curl -SL --output vs_buildtools.exe https://aka.ms/vs/17/release/vs_buildtools.exe `
    `
    # Install Build Tools with the Microsoft.VisualStudio.Workload.AzureBuildTools workload, excluding workloads and components with known issues.
    && (start /w vs_buildtools.exe --quiet --wait --norestart --nocache `
    --installPath "%ProgramFiles(x86)%\Microsoft Visual Studio\2022\BuildTools" `
    --remove Microsoft.VisualStudio.Component.Windows10SDK.10240 `
    --remove Microsoft.VisualStudio.Component.Windows10SDK.10586 `
    --remove Microsoft.VisualStudio.Component.Windows10SDK.14393 `
    --remove Microsoft.VisualStudio.Component.Windows81SDK `
    || IF "%ERRORLEVEL%"=="3010" EXIT 0) `
    `
    # Cleanup
    && del /q vs_buildtools.exe;

# Add MSBuild to PATH
RUN powershell -Command `
    [System.Environment]::SetEnvironmentVariable('Path', $env:Path + ';C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin', [System.EnvironmentVariableTarget]::Machine)

# Install .NET SDK
RUN `
    curl -SL --output dotnet-sdk.exe https://download.visualstudio.microsoft.com/download/pr/f5f1c28d-7bc9-431e-98da-3e2c1bbd1228/864e152e374b5c9ca6d58ee953c5a6ed/dotnet-sdk-8.0.401-win-x64.exe `
    && start /w dotnet-sdk.exe /quiet /norestart `
    && del /q dotnet-sdk.exe
# Add .NET to PATH
RUN setx /M PATH "%PATH%;C:\Program Files\dotnet"

# Install P4 
ENV P4_VERSION=24.2
ENV P4_URL=https://www.perforce.com/downloads/perforce/r${P4_VERSION}/bin.ntx64/p4vinst64.msi

RUN powershell -Command `
    Invoke-WebRequest -Uri $env:P4_URL -OutFile "p4vinst64.msi"; `
    Start-Process -FilePath "p4vinst64.msi" -ArgumentList "/quiet" -Wait; `
    Remove-Item -Force "p4vinst64.msi"; `
    [System.Environment]::SetEnvironmentVariable('Path', $env:Path + ';C:\Program Files\Perforce', [System.EnvironmentVariableTarget]::Machine)

# Install Git
RUN powershell -Command `
    Invoke-WebRequest -Uri "https://github.com/git-for-windows/git/releases/download/v2.46.0.windows.1/Git-2.46.0-64-bit.exe" -OutFile "git-installer.exe"; `
    Start-Process -FilePath "git-installer.exe" -ArgumentList '/VERYSILENT /NORESTART /NOCANCEL /SP- /CLOSEAPPLICATIONS /RESTARTAPPLICATIONS /COMPONENTS="icons,ext\\reg\\shellhere,assoc,assoc_sh"' -Wait; `
    Remove-Item -Force "git-installer.exe"

# Install Python 
RUN powershell -Command `
    Invoke-WebRequest -Uri "https://www.python.org/ftp/python/3.9.7/python-3.9.7-amd64.exe" -OutFile "python-installer.exe"; `
    Start-Process -FilePath "python-installer.exe" -ArgumentList '/quiet InstallAllUsers=1 PrependPath=1' -Wait; `
    Remove-Item -Force "python-installer.exe"

RUN powershell -Command `
    Write-Host "MSBUILD: $(msbuild --version; Write-Host) `n"; `
    Write-Host "DOTNET: $(dotnet --version; Write-Host) `n"; `
    Write-Host "PYTHON: $(python --version; Write-Host) `n"; `
    Write-Host "GIT: $(git --version; Write-Host) `n"; `
    Write-Host "PERFORCE: $(p4 -V; Write-Host) `n";

# Change to your own entry point. Enjoy :)
ENTRYPOINT ["C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\Common7\\Tools\\VsDevCmd.bat", "&&", "powershell.exe", "-NoLogo", "-ExecutionPolicy", "Bypass"]
