# About this repo
Miscellaneous files, notes etc. for working with Windows Containers, Docker, k8s windows nodes

# Windows containers
In 'containerrs\batteries_included' you will find a Docker file with windows image building an image with: 
- msbuild tools
- dotnet sdk
- python
- git
- perforce client 

All above added to env variables ready to handle your CI workloads. Based on [MS docs](https://learn.microsoft.com/en-us/visualstudio/install/build-tools-container?view=vs-2022) instruction for msbuild. 

## Building the container

Remember to switch to 'Windows containers' if you're using Docker Desktop for Windows. Skip if you're on Windows Server. Also check compatibility chart [here](https://learn.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2022%2Cwindows-11). 

![Switch](/doc/img/switch_to_windows_containers.png)

## Run build command, for example: 

```powershell 
docker build -t buildtools:latest . 
```


![Switch](/doc/img/build_result.png)


## Test image locally

```powershell 
docker run -it buildtools 
```

![Switch](/doc/img/container_interactive.png)

### Enjoy!