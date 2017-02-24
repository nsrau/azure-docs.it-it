---
title: Usare Docker Machine per creare host Linux in Azure | Documentazione Microsoft
description: Descrive l&quot;uso di Docker Machine per creare host Docker in Azure.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: b64bafe1a8572369aa7a7e6f0d8b924fc182cd53
ms.openlocfilehash: 9440474db7eb7e3ae082505f8c95cbcf8c285cdf


---
# <a name="use-docker-machine-with-the-azure-driver"></a>Usare Docker Machine con il driver di Azure
[Docker](https://www.docker.com/) fornisce virtualizzazione tramite contenitori Linux invece di macchine virtuali al fine di isolare i dati dell'applicazione ed eseguire i calcoli in una risorsa condivisa. Questo argomento descrive come e quando usare [Docker Machine](https://docs.docker.com/machine/). Il comando `docker-machine` crea una nuova VM Linux in Azure abilitata come host del docker per i contenitori Linux.

## <a name="create-vms-with-docker-machine"></a>Creare VM con Docker Machine
Creare VM host di Docker in Azure con il comando `docker-machine create` usando l'argomento `azure` del driver per l'opzione driver (`-d`) e altri argomenti. 

L'esempio seguente si basa sui valori predefiniti, ma apre la porta 80 della VM verso Internet per eseguire il test con un contenitore nginx, rende `ops` l'utente di accesso per SSH e chiama la nuova VM `machine`. 

Digitare `docker-machine create --driver azure` per vedere le opzioni e i corrispondenti valori predefiniti. È anche possibile leggere la [documentazione del driver di Azure per Docker](https://docs.docker.com/machine/drivers/azure/). Si noti che se è abilitata l'autenticazione a due fattori, verrà richiesto di eseguire l'autenticazione tramite il secondo fattore.

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

Se l'account è configurato per l'autenticazione a due fattori, l'output dovrebbe essere simile a quanto segue.

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configurare la shell di Docker
A questo punto digitare `docker-machine env <VM name>` per vedere quali sono le informazioni necessarie configurare la shell. 

```bash
docker-machine env machine
```

Stampa le informazioni sull'ambiente, che si presentano in modo simile a quanto segue. Si noti che viene assegnato un indirizzo IP, necessario per il test della VM.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

È possibile eseguire il comando di configurazione consigliato oppure impostare le variabili di ambiente manualmente. 

## <a name="run-a-container"></a>Eseguire un contenitore
È ora possibile eseguire un semplice server Web per verificare se tutto funziona correttamente. Qui viene usata un'immagine nginx standard. Specificare che deve essere in ascolto sulla porta 80 e che se la VM viene riavviata, anche il contenitore deve essere riavviato (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

L'output dovrebbe essere simile a quanto segue.

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Eseguire il test del contenitore
Esaminare i contenitori in esecuzione usando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Quindi verificare il contenitore in esecuzione, digitare `docker-machine ip <VM name>` per trovare l'indirizzo IP, se lo si è dimenticato dopo l'esecuzione del comando `env`:

![Esecuzione di un contenitore ngnix](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Passaggi successivi
Se interessa, è possibile provare l' [estensione VM per Docker](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) di Azure per eseguire la stessa operazione tramite l'interfaccia della riga di comando di Azure o i modelli di Azure Resource Manager. 

Per altri esempi di uso di Docker, vedere [Working with Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) (Uso di Docker) dalla demo [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect[](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Per altre guide introduttive della demo HealthClinic.biz, vedere [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Guide introduttive agli strumenti di sviluppo di Azure).




<!--HONumber=Feb17_HO3-->


