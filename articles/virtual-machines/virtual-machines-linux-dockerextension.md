<properties
   pageTitle="Informazioni sull'estensione VM Docker in Azure | Microsoft Azure"
   description="Informazioni su come usare l'estensione di VM Docker per distribuire in modo rapido e sicuro un ambiente di Docker in Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="07/20/2016"
   ms.author="iainfou"/>

# Uso dell'estensione di VM Docker per distribuire l'ambiente

Docker è una nota piattaforma di creazione dell'immagine e gestione di contenitori che consente di lavorare rapidamente con contenitori in Linux (e anche Windows). Con Azure, è possibile distribuire Docker in alcuni modi diversi a seconda delle esigenze:

- Per creare rapidamente il prototipo di un'app, è possibile [Usare Docker Machine con il driver di Azure](./virtual-machines-linux-docker-machine.md) per distribuire host Docker in Azure.
- L'estensione della VM Docker per le macchine virtuali di Azure è usata per le distribuzioni conformi al modello di base. Questo approccio può integrarsi con distribuzioni del modello di Azure Resource Manager e include tutti i vantaggi correlati, ad esempio accesso di base di ruolo, diagnostica e configurazione post-distribuzione.
- L'estensione della VM Docker supporta anche Docker Compose. Docker Compose usa un file YAML dichiarativo per eseguire un'applicazione modellata dallo sviluppatore in qualsiasi ambiente e generare una distribuzione coerente.
- È anche possibile [distribuire un cluster Docker Swarm completo in servizi contenitore di Azure](../container-service/container-service-deployment.md) per distribuzioni scalabili, pronte per l'ambiente di produzione, che usano gli strumenti di pianificazione e gestione aggiuntivi offerti da Swarm.

Questo articolo illustra l'uso di modelli di Resource Manager per distribuire l'estensione di VM Docker in un ambiente personalizzato, di produzione, definito dall'utente.

## Estensione di VM Docker di Azure per distribuzioni di modelli

L'estensione della VM Docker per Azure installa e configura il daemon Docker, il client Docker e Docker Compose nella macchina virtuale Linux. L'estensione viene anche usata per definire e distribuire applicazioni contenitore con Docker Compose. Dal momento che prevede controlli aggiuntivi rispetto all'uso di Docker Machine o alla creazione manuale dell'host Docker, è ideale per ambienti di sviluppo o produzione più affidabili.

Tramite Azure Resource Manager, è possibile creare e distribuire modelli che definiscono l'intera struttura dell'ambiente. I modelli consentono di definire gli host Docker, l'archiviazione, il controllo degli accessi basato sui ruoli, la diagnostica e così via. Per comprendere meglio alcuni dei vantaggi, è possibile [leggere altre informazioni su Resource Manager](../resource-group-overview.md) e sui modelli. Utilizzando i modelli di Resource Manager, sarà anche possibile riprodurre le distribuzioni in base alle esigenze future.

## Distribuire un modello con l'estensione di VM Docker:

Per mostrare come distribuire una VM Ubuntu su cui è installata l'estensione di VM Docker, si userà un modello di avvio rapido esistente. Per visualizzare il modello, vedere [Distribuzione semplice di una VM Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). È necessaria anche la [più recente interfaccia della riga di comando di Azure](../xplat-cli-install.md) in modalità Gestione risorse (`azure config mode arm`).

Distribuire il modello tramite l'interfaccia della riga di comando di Azure specificando un nome per il nuovo gruppo di risorse, in questo caso `myDockerResourceGroup`, insieme all'URI del modello:

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Rispondere alle richieste per la denominazione dell'account di archiviazione, del nome DNS, del nome utente e così via e quindi attendere alcuni minuti il completamento della distribuzione. L'output dovrebbe essere simile al seguente:

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## Distribuire il primo contenitore nginx
Al termine della distribuzione, stabilire una connessione SSH al nuovo host Docker con il nome DNS specificato durante la distribuzione. Si provi a eseguire un contenitore nginx:

```
sudo docker run -d -p 80:80 nginx
```

L'output dovrebbe essere simile al seguente:

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

Se lo si desidera, è possibile configurare la porta TCP del daemon Docker o le impostazioni di sicurezza oppure distribuire contenitori mediante Docker Compose. Per ulteriori informazioni, vedere l'articolo sull'[estensione macchina virtuale di Azure per il progetto GitHub Docker](https://github.com/Azure/azure-docker-extension/).

## Riferimento al modello JSON per l'installazione dell'estensione di VM Docker

In questo esempio viene usato un modello di avvio rapido. Per distribuire l'estensione VM Docker di Azure con i propri modelli di Resource Manager, aggiungere quanto segue:

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

## Passaggi successivi

Leggere passaggi più dettagliati per le diverse opzioni di distribuzione:

1. [Usare Docker Machine con il driver di Azure](./virtual-machines-linux-docker-machine.md)
2. [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](./virtual-machines-linux-classic-cli-use-docker.md)
3. [Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure](virtual-machines-linux-docker-compose-quickstart.md).
3. [Distribuire un cluster del servizio contenitore di Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0817_2016-->