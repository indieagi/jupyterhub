# 1 Create Droplet in Digital Ocean
Choose Ubuntu 22 LTS
 
# 2 Configure Ubuntu VM
## Clone this repo
```
git clone https://github.com/indieagi/jupyterhub-deploy-docker.git
cd jupyterhub-deploy-docker/
```

## Install Docker
```
cd ./indieagi-deploy/install-scripts

chmod +x install-docker.sh start-docker-on-boot.sh
./install-docker.sh
./start-docker-on-boot.sh

```

## Test Docker Install
```
docker run hello-world
```

## Mount Volumes
Create Digital Ocean volume via Digital Ocean console
```
cd ./indieagi-deploy/install-scripts
chmod +x mount-volumes.sh
./mount-volumes.sh
```

## Test Linux Volume Configuration
First, reboot the system
```
reboot
```

Then, after the system reboots run
```
df -h | grep /mnt/jupyterhub_data
```
If output than successful if no output there is a problem.

## Install JupyterHub Server
Run the following commands
```
cd ./indieagi-deploy
docker-compose build
docker-compose up -d
```

## Test JupyterHub Server
### Test Volume Configuration of JupyterHub Docker Container
```
docker inspect jupyterhub | grep /mnt/jupyterhub_data
```
You should see some output similar to this:
```
"Mounts": [
  {
    "Source": "/mnt/jupyterhub_data",
    "Destination": "/data",
    "Mode": "",
    "RW": true,
    "Propagation": "rprivate"
  },
  ...
]
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

# 3 Configure DNS
1. Navigate to the [NameCheap advanced DNS console for indieagi.org](https://ap.www.namecheap.com/Domains/DomainControlPanel/indieagi.org/advancedns)
2. Change the A record for jupyter.indieagi.org to your new Droplet's IP address

## Test DNS Configuration
1. Wait for the DNS records to propogate
2. Navigate to jupyter.indieagi.org. If the login page for JupyterHub loads, DNS is configured correctly

# 4 Configure SSL
todo

## Test SSL Configuration
1. Navigate to jupyter.indieagi.org. Do not use the IP address.
2. Login using a known working user account.
3. If you get a 200 OK and get into your JupyterLab instance, SSL configuration is successful. If you get a 302 Redirect back to login, the JupyterHub SSL configuration is probably wrong.
