# Setup WSL2

## Prerequities

1. [Install WSL](https://docs.microsoft.com/en-us/windows/wsl/install)
2. Install Windows Terminal from the Windows Store
3. Do the following command in Powershell as Administrator

```bash
# Install WSL
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# Enable Virtual Machine Platform
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Use WSL2 per default
wsl --set-default-version 2
```


## Reference

[How to setup WSL2 with Windows Terminal, oh-my-zsh and powerlevel10k](https://yduman.github.io/blog/wsl2-setup/)
