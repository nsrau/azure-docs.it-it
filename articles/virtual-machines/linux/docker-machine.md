---
title: Usare Docker Machine per creare host Linux in Azure | Documentazione Microsoft
description: Descrive l'uso di Docker Machine per creare host Docker in Azure.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.openlocfilehash: efd0b60079017e476b54acfc65ffe8f35ffe4933
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Come usare Docker Machine per creare host in Azure
Questa articolo illustra come usare [Docker Machine](https://docs.docker.com/machine/) per creare host in Azure. Il comando `docker-machine` crea una macchina virtuale (VM) Linux in Azure e quindi installa Docker. Ciò consentirà di gestire gli host Docker in Azure usando gli stessi strumenti e flussi di lavoro locali. Per usare docker-machine in Windows 10, è necessario usare la bash di Linux.

## <a name="create-vms-with-docker-machine"></a>Creare VM con Docker Machine
Ottenere prima di tutto l'ID sottoscrizione di Azure con [az account show](/cli/azure/account#show) nel modo seguente:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Le macchine virtuali host Docker vengono create in Azure con `docker-machine create` specificando *azure* come driver. Per altre informazioni, vedere la [documentazione del driver di Azure per Docker](https://docs.docker.com/machine/drivers/azure/)

Nell'esempio seguente viene creata una macchina virtuale denominata *myVM*, in base al piano "Standard D2 v2", viene creato un account utente denominato *azureuser* e viene aperta la porta *80* sulla macchina virtuale host. Seguire le istruzioni visualizzate per accedere all'account Azure e concedere a Docker Machine le autorizzazioni necessarie per creare e gestire le risorse.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

L'output è simile al seguente esempio:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
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
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>Configurare la shell di Docker
Per connettersi all'host Docker in Azure, definire le impostazioni di connessione appropriate. Come indicato alla fine dell'output, visualizzare le informazioni di connessione per l'host Docker nel modo seguente: 

```bash
docker-machine env myvmdocker
```

L'output è simile all'esempio seguente:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

Per definire le impostazioni di connessione, è possibile eseguire il comando di configurazione suggerito (`eval $(docker-machine env myvmdocker)`) o impostare manualmente le variabili di ambiente. 

## <a name="run-a-container"></a>Eseguire un contenitore
Per vedere un contenitore in azione, verrà eseguito un server Web NGINX di base. Creare un contenitore con `docker run` ed esporre la porta 80 al traffico Web nel modo seguente:

```bash
docker run -d -p 80:80 --restart=always nginx
```

L'output è simile all'esempio seguente:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Visualizzare i contenitori in esecuzione con `docker ps`. Nell'output di esempio seguente si può vedere il contenitore NGINX in esecuzione con la porta 80 esposta:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Eseguire il test del contenitore
Ottenere l'indirizzo IP pubblico dell'host Docker come indicato di seguito:


```bash
docker-machine ip myvmdocker
```

Per vedere il contenitore in azione, aprire un Web browser e immettere l'indirizzo IP pubblico indicato nell'output del comando precedente:

![Esecuzione di un contenitore ngnix](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Passaggi successivi
È anche possibile creare host con l'[estensione di VM Docker](dockerextension.md). Per esempi sull'uso di Docker Compose, vedere [Introduzione a Docker e Compose in Azure](docker-compose-quickstart.md).
