# Miniconda 설치

## Install miniconda

- Linux

```bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh
bash ~/miniconda.sh -b -p
rm ~/miniconda.sh
source $HOME/miniconda3/bin/activate
conda --help
```

- Mac

```bash
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -o ~/miniconda.sh
bash ~/miniconda.sh -b -p
rm ~/miniconda.sh
source $HOME/miniconda3/bin/activate
conda --help
```

- Windows

```bash
powershell -command "Invoke-WebRequest -Uri https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe -OutFile ~\miniconda.exe"
start /B /WAIT %UserProfile%\miniconda.exe /InstallationType=JustMe /AddToPath=0 /RegisterPython=0 /S /D=%UserProfile%\miniconda3
del %UserProfile%\miniconda.exe
%UserProfile%\miniconda3\condabin\activate
conda --help
```
