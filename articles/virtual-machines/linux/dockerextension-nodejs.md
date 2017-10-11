---
title: Usare l'estensione di macchina virtuale Docker in Azure con l'interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come usare l'estensione di VM Docker per distribuire in modo rapido e sicuro un ambiente Docker in Azure con i modelli di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a3cbcf63533f4042dcd695e141655c5814bd7068
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Creare un ambiente Docker in Azure usando l'estensione di macchina virtuale Docker con l'interfaccia della riga di comando di Azure 1.0
Docker è una nota piattaforma di creazione dell'immagine e gestione di contenitori che consente di lavorare rapidamente con contenitori in Linux (e anche Windows). L'ambiente Azure offre vari modi per distribuire Docker a seconda delle proprie esigenze. Questo articolo si concentra sull'uso dell'estensione di VM Docker e dei modelli di Azure Resource Manager. 

Per altre informazioni sui diversi metodi di distribuzione, incluso l'uso di Docker Machine e i servizi contenitore di Azure, vedere gli articoli seguenti:

* Per creare rapidamente il prototipo di un'applicazione, è possibile creare un singolo host di Docker usando [Docker Machine](docker-machine.md).
* Per gli ambienti più stabili ed estesi è possibile usare l'estensione di VM Docker di Azure che supporta anche [Docker Compose](https://docs.docker.com/compose/overview/) per generare distribuzioni di contenitore coerenti. Questo articolo descrive come usare l'estensione di VM Docker di Azure.
* Per creare ambienti scalabili e pronti per la produzione che offrono maggiori strumenti di pianificazione e gestione, è possibile distribuire un [cluster Docker Swarm nei servizi contenitore di Azure](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#azure-docker-vm-extension-overview): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](dockerextension.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa 

## <a name="azure-docker-vm-extension-overview"></a>Panoramica dell'estensione di VM Docker di Azure
L'estensione di VM Docker di Azure installa e configura il daemon Docker, il client Docker e Docker Compose nella macchina virtuale (VM) Linux. L'uso dell'estensione di VM Docker di Azure permette di ottenere un livello di controllo superiore e un maggior numero di funzionalità rispetto a quanto offerto dal semplice uso di Docker Machine o dalla creazione di un host Docker. Questi funzionalità aggiuntive, come ad esempio [Docker Compose](https://docs.docker.com/compose/overview/), rendono l'estensione di VM Docker di Azure la soluzione ideale per ambienti di sviluppo o di produzione più solidi.

I modelli di Azure Resource Manager definiscono l'intera struttura dell'ambiente. I modelli consentono di creare e configurare le risorse, ad esempio le VM dell'host Docker, l'archiviazione, i controlli degli accessi in base al ruolo (RBAC) e la diagnostica. È possibile riusare questi modelli per creare distribuzioni aggiuntive in modo coerente. Per altre informazioni su Azure Resource Manager e relativi modelli, vedere la [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Distribuire un modello con l'estensione di VM Docker di Azure
Usare un modello di avvio rapido esistente per creare una macchina virtuale Ubuntu che usa l'estensione di VM Docker di Azure per installare e configurare l'host Docker. Per visualizzare il modello, vedere [Distribuzione semplice di una VM Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

È necessario installare e registrare l'[interfaccia della riga di comando Azure più recente](../../cli-install-nodejs.md) usando la modalità di Resource Manager nel modo seguente:

```azurecli
azure config mode arm
```

Distribuire il modello con l'interfaccia della riga di comando di Azure, specificando l'URI del modello. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *westus*. Usare il nome e la posizione del gruppo di risorse nel modo seguente:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Rispondere ai prompt di richiesta di rinominare l'account di archiviazione, fornire il nome utente e la password e specificare il nome DNS. L'output è simile all'esempio seguente:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

L'interfaccia della riga di comando di Azure fa tornare l'utente al menu principale di prompt dopo alcuni secondi, ma l'host Docker viene comunque creato e configurato dall'estensione di VM Docker di Azure. L'operazione di distribuzione richiede alcuni minuti. È possibile visualizzare i dettagli sullo stato dell'host Docker con il comando `azure vm show`.

L'esempio seguente controlla lo stato della macchina virtuale denominata *myDockerVM* (il nome predefinito del modello, non modificare questo nome) che appartiene al gruppo di risorse *myResourceGroup*. Immettere il nome del gruppo di risorse creato nel passaggio precedente:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

L'output del comando `azure vm show` sarà simile all'esempio seguente:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Nella parte superiore dell'output viene visualizzato il valore **ProvisioningState** della VM. Quando viene visualizzato *Succeeded*, la distribuzione è stata completata ed è possibile usare SSH per la VM.

Verso la fine dell'output, *FQDN* visualizza il nome di dominio completo dell'host Docker. Questo è l'FQDN che si usa per accedere tramite SSH all'host Docker nei passaggi successivi.

## <a name="deploy-your-first-nginx-container"></a>Distribuire il primo contenitore nginx
Al termine della distribuzione, accedere tramite SSH al nuovo host Docker dal computer locale. Immettere il nome utente e l'FQDN nel modo seguente:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Dopo aver effettuato l'accesso all'host Docker, eseguire un contenitore nginx:

```bash
sudo docker run -d -p 80:80 nginx
```

L'output è simile all'esempio seguente quando l'immagine nginx viene scaricata e un contenitore viene avviato:

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

L'output è simile all'esempio seguente, in cui viene mostrato che il contenitore nginx è in esecuzione e viene eseguito l'inoltro alle porte TCP 80 e 443:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Per vedere il contenitore in azione, aprire un Web browser e immettere il nome FQDN dell'host Docker:

![Esecuzione di un contenitore ngnix](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Riferimento al modello dell'estensione di VM Docker di Azure
Nell'esempio precedente è stato usato un modello di avvio rapido esistente. È anche possibile distribuire l'estensione di VM Docker di Azure con i propri modelli di Resource Manager. Per questo scopo, aggiungere quanto segue ai modello di Resource Manager, definendo in modo appropriato il *vmName* della VM:

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
    "typeHandlerVersion": "1.1",
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

