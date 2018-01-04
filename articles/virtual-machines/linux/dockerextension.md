---
title: Usare l'estensione di VM Docker di Azure | Documentazione Microsoft
description: Informazioni su come usare l'estensione di VM Docker per distribuire in modo rapido e sicuro un ambiente Docker in Azure con i modelli di Resource Manager e l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: ce44a5e4db080822aaec0b50a265b863059bd45a
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Creare un ambiente Docker in Azure usando l'estensione di VM Docker
Docker è una nota piattaforma di creazione dell'immagine e gestione di contenitori che consente di lavorare rapidamente con contenitori in Linux. L'ambiente Azure offre vari modi per distribuire Docker a seconda delle proprie esigenze. Questo articolo è incentrato sull'uso dell'estensione di VM Docker e dei modelli di Azure Resource Manager con l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](dockerextension-nodejs.md).

## <a name="azure-docker-vm-extension-overview"></a>Panoramica dell'estensione di VM Docker di Azure
L'estensione di VM Docker di Azure installa e configura il daemon Docker, il client Docker e Docker Compose nella macchina virtuale (VM) Linux. L'uso dell'estensione di VM Docker di Azure permette di ottenere un livello di controllo superiore e un maggior numero di funzionalità rispetto a quanto offerto dal semplice uso di Docker Machine o dalla creazione di un host Docker. Questi funzionalità aggiuntive, come ad esempio [Docker Compose](https://docs.docker.com/compose/overview/), rendono l'estensione di VM Docker di Azure la soluzione ideale per ambienti di sviluppo o di produzione più solidi.

Per altre informazioni sui diversi metodi di distribuzione, incluso l'uso di Docker Machine e i servizi contenitore di Azure, vedere gli articoli seguenti:

* Per creare rapidamente il prototipo di un'applicazione, è possibile creare un singolo host di Docker usando [Docker Machine](docker-machine.md).
* Per creare ambienti di ambiente di produzione e scalabili che forniscono gli strumenti di pianificazione e di gestione aggiuntivi, è possibile distribuire un [Kubernetes](../../container-service/kubernetes/index.yml) o [Docker Swarm](../../container-service/dcos-swarm/index.yml) cluster in servizi di contenitore di Azure.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Distribuire un modello con l'estensione di VM Docker di Azure
Usare un modello di avvio rapido esistente per creare una macchina virtuale Ubuntu che usa l'estensione di VM Docker di Azure per installare e configurare l'host Docker. Per visualizzare il modello, vedere [Distribuzione semplice di una VM Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). È necessario aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Successivamente, distribuire una macchina virtuale con il comando [az group deployment create](/cli/azure/group/deployment#create) che include l'estensione di VM Docker di Azure da [questo modello di Azure Resource Manager su GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Quando richiesto, fornire i propri valori univoci per *newStorageAccountName*, *adminUsername*, *adminPassword*, e *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

L'operazione di distribuzione richiede alcuni minuti.


## <a name="deploy-your-first-nginx-container"></a>Distribuire il primo contenitore NGINX
Per visualizzare i dettagli della VM, incluso il nome DNS, usare [az vm show](/cli/azure/vm#show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH per il nuovo host Docker. Fornire il proprio nome utente e nome DNS nei passaggi precedenti:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Dopo aver effettuato l'accesso all'host Docker, eseguire un contenitore NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

L'output è simile all'esempio seguente quando l'immagine NGINX viene scaricata e un contenitore viene avviato:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Per controllare lo stato dei contenitori in esecuzione sull'host Docker, fare come segue:

```bash
sudo docker ps
```

L'output è simile all'esempio seguente, in cui viene mostrato che il contenitore NGINX è in esecuzione e viene eseguito l'inoltro alle porte TCP 80 e 443:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Per vedere il contenitore in azione, aprire un Web browser e immettere il nome DNS dell'host Docker:

![Esecuzione di un contenitore ngnix](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Riferimento al modello dell'estensione di VM Docker di Azure
Nell'esempio precedente è stato usato un modello di avvio rapido esistente. È anche possibile distribuire l'estensione di VM Docker di Azure con i propri modelli di Resource Manager. Per farlo, aggiungere quanto segue ai modello di Resource Manager, definendo in modo appropriato `vmName` della VM:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Per altre procedure dettagliate relative all'uso di modelli di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Passaggi successivi
Se lo si desidera, [configurare la porta TCP del daemon Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprendere le [informazioni di sicurezza di Docker](https://docs.docker.com/engine/security/security/) oppure distribuire i contenitori con [Docker Compose](https://docs.docker.com/compose/overview/). Per altre informazioni sull'estensione di VM Docker di Azure, vedere il [Progetto GitHub](https://github.com/Azure/azure-docker-extension/).

Per altre informazioni sulle opzioni di distribuzione di Docker aggiuntive in Azure, leggere:

* [Usare Docker Machine con il driver di Azure](docker-machine.md)  
* [Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure](docker-compose-quickstart.md).
* [Distribuire un cluster del servizio contenitore di Azure](../../container-service/dcos-swarm/container-service-deployment.md)

