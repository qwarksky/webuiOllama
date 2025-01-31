# Installation de WebUI + OLLAMA + Mistral


## documentation : 
* WebUI docker: https://docs.openwebui.com/getting-started/quick-start/
* CUDA dirvers : https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#prerequisites
* Ollama docs : https://github.com/ollama/ollama/blob/main/docs/README.md
* Webui+Ollama : https://docs.openwebui.com/getting-started/quick-start/starting-with-ollama
* Debug : https://github.com/open-webui/open-webui#troubleshooting



# Commandes Docker pour l'utilisation WebUI

### PULL
  > docker pull ghcr.io/open-webui/open-webui:main

### UPDATE
  > docker run --rm --volume /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --run-once open-webui

### DELETE WEBUI
  > docker rm -f open-webui

### RUN NO LOGIN
  > docker run -d -p 3000:8080 -e WEBUI_AUTH=False -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main

### RUN WITH GPU no Password
  > docker run -d -p 3000:8080 -e WEBUI_AUTH=False --gpus all -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:cuda

### BUNDLED
  > docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama



### Copy API KEY OLLAMA 

  > Option User > Settings > Admin Settings > Connections > "Manage OpenAI API Connections => Paste  
cat /usr/share/ollama/.ollama/PUBLICSKEYS.pub

### Pouvoir récupérer les modèles depuis Ollama 
  > docker run -d --network=host -e WEBUI_AUTH=False -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 --name open-webui --restart always ghcr.io/open-webui/open-webui:main


# Déplacement Dossier images Docker sur un disque Nvme

1. Détecter enmplacement par défaut
	> docker info
	> Docker Root Dir: /var/lib/docker

2. Stopper docker
	> sudo systemctl stop docker.service
	> sudo systemctl stop docker.socket
	
3. BKP
	> sudo cp /lib/systemd/system/docker.service /lib/systemd/system/docker.serviceBKP

4. EDITER
	> sudo vi /lib/systemd/system/docker.service

	Ajouter --data-root à la ligne
	>ExecStart=/usr/bin/dockerd --data-root /media/nvme/DOCKERS/.IMAGESBASE -H fd:// --containerd=/run/containerd/containerd.sock
	[save]

5. Déplacement des images 
	> sudo rsync -aqxP /var/lib/docker/ /media/nvme/DOCKERS/.IMAGESBASE 

6. Redémarrage de Docker
	> sudo systemctl daemon-reload
	> sudo systemctl start docker

# Déplacement des modèles de Ollama sur Nvme

1. Editer le service Ollama
	> sudo systemctl edit ollama.service
2. Décommenter la ligne et indiquer la destination des modèles
 	> [Service]
 	> Environment="OLLAMA_MODELS=/media/nvme/MODELSIA/ollama"

3. Redémarrage du service Ollama
	> sudo systemctl daemon-reload
	> sudo systemctl restart ollama



# ACCESS WEBUIS

* Webaccess OpenWebUI => http://localhost:3000/
* Python deployement => http://localhost:8080/
