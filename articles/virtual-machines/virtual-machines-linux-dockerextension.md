---
title: Informazioni sull'estensione VM Docker in Azure | Microsoft Docs
description: Informazioni su come usare l'estensione di VM Docker per distribuire in modo rapido e sicuro un ambiente di Docker in Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/10/2016
ms.author: iainfou

---
# <a name="using-the-docker-vm-extension-to-deploy-your-environment"></a>Uso dell'estensione di VM Docker per distribuire l'ambiente
Docker è una nota piattaforma di creazione dell'immagine e gestione di contenitori che consente di lavorare rapidamente con contenitori in Linux (e anche Windows). Con Azure, è possibile distribuire Docker in alcuni modi diversi a seconda delle esigenze:

* Per creare rapidamente il prototipo di un'app, è possibile [Usare Docker Machine con il driver di Azure](virtual-machines-linux-docker-machine.md) per distribuire host Docker in Azure.
* L'estensione della VM Docker per le macchine virtuali di Azure è usata per le distribuzioni conformi al modello di base. Questo approccio può integrarsi con distribuzioni del modello di Azure Resource Manager e include tutti i vantaggi correlati, ad esempio accesso in base al ruolo, diagnostica e configurazione post-distribuzione.
* L'estensione della VM Docker supporta anche Docker Compose. Docker Compose usa un file YAML dichiarativo per eseguire un'applicazione modellata dallo sviluppatore in qualsiasi ambiente e generare una distribuzione coerente.
* È anche possibile [distribuire un cluster Docker Swarm completo in servizi contenitore di Azure](../container-service/container-service-deployment.md) per distribuzioni scalabili, pronte per l'ambiente di produzione, che usano gli strumenti di pianificazione e gestione aggiuntivi offerti da Swarm.

Questo articolo illustra l'uso di modelli di Resource Manager per distribuire l'estensione di VM Docker in un ambiente personalizzato, di produzione, definito dall'utente.

## <a name="azure-docker-vm-extension-for-template-deployments"></a>Estensione di VM Docker di Azure per distribuzioni di modelli
L'estensione della VM Docker per Azure installa e configura il daemon Docker, il client Docker e Docker Compose nella macchina virtuale Linux. L'estensione viene anche usata per definire e distribuire applicazioni contenitore con Docker Compose. Dal momento che prevede controlli aggiuntivi rispetto all'uso di Docker Machine o alla creazione manuale dell'host Docker, è ideale per ambienti di sviluppo o produzione più affidabili.

Tramite Azure Resource Manager, è possibile creare e distribuire modelli che definiscono l'intera struttura dell'ambiente. I modelli consentono di definire gli host Docker, l'archiviazione, il controllo degli accessi basato su ruoli, la diagnostica e così via. Per comprendere meglio alcuni dei vantaggi, è possibile [leggere altre informazioni su Resource Manager](../resource-group-overview.md) e sui modelli. Usando i modelli di Resource Manager è possibile anche riprodurre le distribuzioni in base alle esigenze future.

## <a name="deploy-a-template-with-the-docker-vm-extension:"></a>Distribuire un modello con l'estensione di VM Docker:
Per mostrare come distribuire una VM Ubuntu su cui è installata l'estensione di VM Docker, si userà un modello di avvio rapido esistente. Per visualizzare il modello, vedere [Distribuzione semplice di una VM Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). È necessaria anche l'[ultima interfaccia della riga di comando di Azure](../xplat-cli-install.md) in modalità Resource Manager (`azure config mode arm`).

Distribuire il modello tramite l'interfaccia della riga di comando di Azure specificando un nome per il nuovo gruppo di risorse, in questo caso `myDockerResourceGroup`, insieme all'URI del modello:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Rispondere ai prompt di richiesta di denominazione dell'account di archiviazione, del nome utente e della password e del nome DNS. L'output dovrebbe essere simile all'esempio seguente:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
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

L'interfaccia della riga di comando di Azure torna al prompt dopo pochi secondi, ma in background il modello viene distribuito nel gruppo di risorse creato. Attendere alcuni minuti il completamento della distribuzione prima di tentare di usare SSH per l'accesso alla VM.

È possibile ottenere informazioni dettagliate sulla distribuzione e il nome DNS della VM usando il comando `azure vm show` . Nell'esempio seguente, sostituire `myResourceGroup` con il nome specificato nel passaggio precedente. Il nome predefinito della macchina virtuale nel modello è `myDockerVM`. Non apportare modifiche:

```bash
azure vm show -g myResourceGroup -n myDockerVM
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/mydockerresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
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
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Nella parte superiore dell'output viene visualizzato il valore `ProvisioningState` della VM. Quando viene visualizzato `Succeeded`, la distribuzione è stata completata ed è possibile usare SSH per la VM.

Verso la fine dell'output, `FQDN` visualizza il nome di dominio completo in base al nome DNS immesso e al percorso selezionato. Questo è l'FQDN che si usa per accedere tramite SSH alla VM nei passaggi successivi.

## <a name="deploy-your-first-nginx-container"></a>Distribuire il primo contenitore nginx
Al termine della distribuzione, stabilire una connessione SSH al nuovo host Docker con il nome DNS specificato durante la distribuzione:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Si provi a eseguire un contenitore nginx:

```
sudo docker run -d -p 80:80 nginx
```

L'output dovrebbe essere simile all'esempio seguente:

```
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

Esaminare il contenitore in esecuzione nell'host usando `sudo docker ps`:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Per vedere il contenitore in azione, aprire un Web browser e immettere il nome DNS specificato durante la distribuzione:

![Esecuzione di un contenitore ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Se lo si desidera, è possibile configurare la porta TCP del daemon Docker o le impostazioni di sicurezza oppure distribuire contenitori mediante Docker Compose. Per altre informazioni, vedere l'articolo sull' [estensione macchina virtuale di Azure per il progetto GitHub Docker](https://github.com/Azure/azure-docker-extension/).

## <a name="docker-vm-extension-json-template-reference"></a>Riferimento al modello JSON per l'installazione dell'estensione di VM Docker
In questo esempio viene usato un modello di avvio rapido. Per distribuire l'estensione VM Docker di Azure con i propri modelli di Resource Manager, aggiungere il JSON seguente:

```
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

Per altre procedure dettagliate relative all'uso di modelli di Resource Manager, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md)

## <a name="next-steps"></a>Passaggi successivi
Leggere passaggi più dettagliati per le diverse opzioni di distribuzione:

1. [Usare Docker Machine con il driver di Azure](virtual-machines-linux-docker-machine.md)  
2. [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](virtual-machines-linux-classic-cli-use-docker.md)  
3. [Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure](virtual-machines-linux-docker-compose-quickstart.md).
4. [Distribuire un cluster del servizio contenitore di Azure](../container-service/container-service-deployment.md)

<!--HONumber=Oct16_HO2-->


