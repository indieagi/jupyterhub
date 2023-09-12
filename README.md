# User & Admin Onboarding
Email toby [at] indieagi [dot] org

# Server Descriptions
```
Static IP        DNS                       Description
146.190.112.121  jupyter.indieagi.org      Stable prod env  
146.190.153.28   jupyter-dev.indieagi.org  Unstable dev env  
```

# Admin Consoles
[Digital Ocean - JupyterHub Prod](https://cloud.digitalocean.com/projects/56c8818b-0d9d-4987-9f19-dd2e8d1cc76b/resources?i=1bc925)  
[Digital Ocean - JupyterHub Dev](https://cloud.digitalocean.com/projects/85397053-3e63-4e04-a7ea-17801702c52d/resources?i=1bc925)  
[NameCheap DNS - indieagi.org](https://ap.www.namecheap.com/domains/domaincontrolpanel/indieagi.org/domain)

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

# Deployment
## 1 Reinstall Kernel on VM
You can usually skip this step if you haven't changed anything in `install-scripts`.

First, go to the appropriate Digital Ocean admin console:  
- [JupyterHub Prod](https://cloud.digitalocean.com/projects/56c8818b-0d9d-4987-9f19-dd2e8d1cc76b/resources?i=1bc925)  
- [JupyterHub Dev](https://cloud.digitalocean.com/projects/85397053-3e63-4e04-a7ea-17801702c52d/resources?i=1bc925) 

Next, click on the Droplet. It will be `jupyterhub-dev` or `jupyterhub-prod`.

Go to Destroy > Rubuild Droplet > Ubuntu 22.04 LTS > Rebuild
 
## 2 Configure Ubuntu VM
### ssh into the VM
```
# Option 1: Go to the Digital Ocean admin console and use the web ssh terminal

# Option 2: Use ssh on your desktop
ssh 146.190.112.121  # JupyterHub Prod
ssh 146.190.153.28   # JupyterHub Dev
```
### Clone this repo
```
git clone https://github.com/indieagi/jupyterhub.git
cd jupyterhub/
```

### Set-up Environment Variables

Run:
```
cd install-scripts
chmod +x *.sh
```

Then, if you are setting up the *Dev environment*, run:
```
./set-dev-env-variables.sh
```
Else, if you are setting up the *Prod environment*, run:
```
./set-prod-env-variables.sh
```

### Install Docker
```
cd ./install-scripts

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
cd ./runtime-config
docker-compose build
docker-compose up -d  # starts JupyterHub in the background
```

### User Test of JupyterHub
1. Navigate to the relevant IP address  
- Prod: [146.190.112.121:8000](http://146.190.112.121:8000)  
- Dev: [146.190.153.28:8000](http://146.190.153.28:8000) 
2. The JupyterHub login screen should be presented

## 3 Configure DNS
1. Navigate to the [NameCheap advanced DNS console for indieagi.org](https://ap.www.namecheap.com/Domains/DomainControlPanel/indieagi.org/advancedns)
2. Change the A record for jupyter.indieagi.org to your new Droplet's IP address

### Test DNS Configuration
1. Wait for the DNS records to propogate
2. Navigate to jupyter.indieagi.org. If the login page for JupyterHub loads, DNS is configured correctly

## 4 Configure SSL
1. Open ./runtime-config/docker-compose.yml
2. Change host to desired domain ```- "traefik.http.routers.jupyterhub.rule=Host(`example.org`)"```
3. Change email for Let's Encrypt to desired email ```- "--certificatesresolvers.myresolver.acme.email=test@example.org"```

### Test SSL Configuration
1. Navigate to jupyter.indieagi.org. Do not use the IP address.
2. Login using a known working user account.
3. If you get a 200 OK and get into your JupyterLab instance, SSL configuration is successful. If you get a 302 Redirect back to login, the JupyterHub SSL configuration is probably wrong.

# Random
## Tail JupyterHub Logs
```
cd ./runtime-config
docker-compose build
docker-compose up -d
docker logs -f jupyterhub
```
## Login Doesn't Work for Known Valid User
1. Check the Chrome Dev Tools Network logs during login
2. If you get a 302, it may be an SSL configuration issue
