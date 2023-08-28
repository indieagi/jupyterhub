# Server Descriptions
```
Static IP        DNS                       Description
146.190.112.121  jupyter.indieagi.org      Stable prod env  
xxx.xxx.xxx.xxx  jupyter-dev.indieagi.org  Unstable dev env [todo]  
```

# File Descriptions
```
./jupyter-deployment/
├── install-scripts  # scripts to run once on a new VM instance
│   ├── install-docker.sh
│   └── start-docker-on-boot.sh
├── LICENSE
├── README.md
└── runtime-config  # configuration used when starting JupyterHub
    ├── docker-compose.yml
    ├── Dockerfile.jupyterhub
    └── jupyterhub_config.py
```


## 1 Create Droplet in Digital Ocean
Choose Ubuntu 22 LTS
 
## 2 Configure Ubuntu VM
### ssh into the VM
```
# Option 1: Go to the Digital Ocean console and use the web ssh terminal

# Option 2: Use ssh on your desktop
ssh 146.190.112.121
```
### Clone this repo
```
git clone https://github.com/indieagi/jupyterhub-deploy-docker.git
cd jupyterhub-deploy-docker/
```

### Install Docker
```
cd ./indieagi-deploy/install-scripts

chmod +x install-docker.sh start-docker-on-boot.sh
./install-docker.sh
./start-docker-on-boot.sh

```

### Test Docker Install
```
docker run hello-world
```

### Install JupyterHub Server
Run the following commands
```
cd ./indieagi-deploy
docker-compose build
docker-compose up -d
```

### User Test of JupyterHub
1. Navigate to ip address in Digital Ocean
2. The JupyterHub login screen should be presented

### Test that Notebooks are in the correct directory
1. Login to your user in JupyterHub
2. Create a new notebook called "hello_world.ipynb"
3. ssh into the Droplet
4. Run the following command to sanity check if the notebook was created. You will have to check subdirectories.
```
ls /mnt/jupyterhub_data
```

## 3 Configure DNS
1. Navigate to the [NameCheap advanced DNS console for indieagi.org](https://ap.www.namecheap.com/Domains/DomainControlPanel/indieagi.org/advancedns)
2. Change the A record for jupyter.indieagi.org to your new Droplet's IP address

### Test DNS Configuration
1. Wait for the DNS records to propogate
2. Navigate to jupyter.indieagi.org. If the login page for JupyterHub loads, DNS is configured correctly

## 4 Configure SSL
todo

### Test SSL Configuration
1. Navigate to jupyter.indieagi.org. Do not use the IP address.
2. Login using a known working user account.
3. If you get a 200 OK and get into your JupyterLab instance, SSL configuration is successful. If you get a 302 Redirect back to login, the JupyterHub SSL configuration is probably wrong.
