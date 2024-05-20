# Install WSL
```sh
wsl --set-default-version 2
wsl --update
wsl --install -d Ubuntu-22.04 

# Update Ubuntu
```sh
sudo apt update && sudo apt -y upgrade
```

# Get CUDA working on WSL
```sh
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.4.1/local_installers/cuda-repo-wsl-ubuntu-12-4-local_12.4.1-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-4-local_12.4.1-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-4
```

# uninstall and reinstall docker
```sh
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

# Add Docker's official GPG key:
```sh
sudo apt-get update && sudo apt-get install ca-certificates curl && sudo install -m 0755 -d /etc/apt/keyrings && sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && sudo chmod a+r /etc/apt/keyrings/docker.asc
```

# Add the repository to Apt sources
```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

# Update the packages list from the repository
```sh
sudo apt-get update
```

# To install the latest version of Docker
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Verify that the Docker Engine installation is successful by running the hello-world image.
sudo docker run hello-world

# Configure the production repository
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

# Optionally, configure the repository to use experimental packages
sudo sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list

# Update the packages list from the repository
sudo apt-get update

# Install the NVIDIA Container Toolkit packages
sudo apt-get install -y nvidia-container-toolkit

# Install dependencies
sudo apt install -y python3 git curl npm pipx python3-pip && pipx ensurepath && pipx install poetry && pip install communex && sudo npm install pm2 -g

# Update path
export PATH="$HOME/.local/bin:$PATH"

# Permanently add to the path
nano ~/.bashrc 

# Test comx (you need to restart the shell first for communex to work)
comx module list

# create key, replace <key-name> with the name you want
comx key create <key-name>

# useful command, find seed for created key. Replace <key-name>.
comx key show --show-private <key-name>

# clone this project
git clone https://github.com/mosaicx-org/mosaic-subnet
cd mosaic-subnet

# start virtualenv and enter it
poetry shell

# install dependencies
poetry install

# Get public IP
curl -4 https://ipinfo.io/ip

# Register a module, replace <module-name> with the name of your module, replace <key> with the name of your key, replace <ip> with public IP of your miner, <port> with the port you want to use and <netuid> with 14 for Mosaic mainnet or 13 for testnet.
comx module register <module-name> <key> --netuid=<netuid> --ip=<ip> --port=<port>


comx module register my-miner-go mykey --netuid=14 --ip=213.164.201.138 --port=8200

# Miner setup, relace <host> with 0.0.0.0 to allow connection from anywhere, replace <port> with same port as registered module
python mosaic_subnet/cli.py [--testnet] [--log-level=INFO] miner <your_commune_key> <host> <port>


python mosaic_subnet/cli.py --log-level=INFO miner mykey 0.0.0.0 8200



















