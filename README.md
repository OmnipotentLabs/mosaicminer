# Setup Commune on Windows 10 Pro/Enterprise without Docker Desktop


### Install WSL
- Click the Start menu and open Powershell as admin
- Set the default version of WSL
- Install Ubuntu-22.04

```sh
wsl --set-default-version 2
```
```sh
wsl --update
```
```sh
wsl --install -d Ubuntu-22.04 
```

### Update Ubuntu
```sh
sudo apt update && sudo apt -y upgrade
```

### Get CUDA working on WSL
```sh
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
```
```sh
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
```
```sh
wget https://developer.download.nvidia.com/compute/cuda/12.4.1/local_installers/cuda-repo-wsl-ubuntu-12-4-local_12.4.1-1_amd64.deb
```
```sh
sudo dpkg -i cuda-repo-wsl-ubuntu-12-4-local_12.4.1-1_amd64.deb
```
```sh
sudo cp /var/cuda-repo-wsl-ubuntu-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
```
```sh
sudo apt-get update
```
```sh
sudo apt-get -y install cuda-toolkit-12-4
```

### Docker setup
- Uninstall older versions of docker
```sh
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

- Add Docker's official GPG key
```sh
sudo apt-get update && sudo apt-get install ca-certificates curl && sudo install -m 0755 -d /etc/apt/keyrings && sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && sudo chmod a+r /etc/apt/keyrings/docker.asc
```

- Add the repository to Apt sources
```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

- Update the packages list from the repository
```sh
sudo apt-get update
```

- To install the latest version of Docker
```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

- Verify that the Docker Engine installation is successful by running the hello-world image
```sh
sudo docker run hello-world
```

### Installing the NVIDIA Container Toolkit
- Configure the production repository
```sh
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

- Optionally, configure the repository to use experimental packages
```sh
sudo sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

- Update the packages list from the repository
```sh
sudo apt-get update
```

- Install the NVIDIA Container Toolkit packages
```sh
sudo apt-get install -y nvidia-container-toolkit
```


### Install dependencies
```sh
sudo apt install -y python3 git curl npm pipx python3-pip && pipx ensurepath && pipx install poetry && pip install communex && sudo npm install pm2 -g
```

- Update path
```sh
export PATH="$HOME/.local/bin:$PATH"
```

- Permanently add to the path
```sh
nano ~/.bashrc 
```


### Test comx (you need to restart the shell first for communex to work)
```sh
comx module list
```

### Create key
- Replace ```<key-name>``` with the name you want
```sh
comx key create <key-name>
```

### Useful command, find seed for newly created key
- Replace ```<key-name>```

```sh
comx key show --show-private <key-name>
```


### Setup Mosaic
- Clone Mosaic repo
```sh
git clone https://github.com/mosaicx-org/mosaic-subnet
```

- Open Mosaic folder
```sh
cd mosaic-subnet
```

- Start virtualenv and enter it
```sh
poetry shell
```

- Install dependencies for Mosaic
```sh
poetry install
```

- Get your public IP
```sh
curl -4 https://ipinfo.io/ip
```

### Register a module
1. Replace ```<module-name>``` with the name of your module
2. Replace ```<key-name>``` with the name of your key
3. Replace ```<netuid>``` with 14 for Mosaic mainnet or 13 for testnet
4. Replace ```<ip>``` with public IP of your miner
5. Replace ```<port>``` with the port you want to use

```sh
comx module register <module-name> <key-name> --netuid=<netuid> --ip=<ip> --port=<port>
```

### Miner setup
1. Replace ```<key-name>``` with your key
2. Replace ```<host>``` with 0.0.0.0 to allow connection from any IP
3. Replace ```<port>``` with the same port as the registered module

```sh
python mosaic_subnet/cli.py --log-level=INFO miner <key-name> <host> <port>
```
