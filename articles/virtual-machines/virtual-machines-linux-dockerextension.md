<properties
   pageTitle="Informazioni sull'estensione di VM Docker in Azure | Microsoft Azure"
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

- Per creare rapidamente il prototipo di un'app o se già si conosce e si usa Docker Machine, è possibile [Usare Docker Machine con il driver di Azure](./virtual-machines-linux-docker-machine.md) per distribuire host Docker in Azure.
- Per una distribuzione del modello di base, è possibile usare l'estensione della VM Docker per le macchine virtuali di Azure. Questo approccio può integrarsi con distribuzioni del modello di Azure Resource Manager e include tutti i vantaggi correlati, ad esempio accesso di base di ruolo, diagnostica e configurazione post-distribuzione.
- L'estensione della VM Docker supporta anche Docker Compose, che usa un file YAML dichiarativo per eseguire un'applicazione modellata dallo sviluppatore in qualsiasi ambiente e generare una distribuzione coerente.
- È anche possibile [distribuire un cluster Docker Swarm completo in servizi contenitore di Azure](../container-service/container-service-deployment.md) per distribuzioni scalabili, pronte per l'ambiente di produzione, che sfruttano gli strumenti di pianificazione e gestione aggiuntivi offerti da Swarm.

Questo articolo illustra l'uso di modelli di Resource Manager per distribuire l'estensione di VM Docker in un ambiente personalizzato, di produzione, definito dall'utente.

## Estensione di VM Docker di Azure per distribuzioni di modelli

L'estensione della VM Docker per Azure installa e configura il daemon Docker, il client Docker e Docker Compose nella macchina virtuale Linux. È anche possibile usare l'estensione per definire e distribuire applicazioni contenitore con Docker Compose. Tramite modelli di Resource Manager, l'ambiente può quindi essere nuovamente distribuito in modo coerente. L'uso dell'estensione della VM Docker per Azure è ideale per ambienti di sviluppo o produzione più affidabili, dal momento che prevede alcuni controlli aggiuntivi rispetto al semplice uso di Docker Machine o alla creazione manuale dell'host Docker.

Con Azure Resource Manager è possibile creare e distribuire modelli che definiscono l'intera struttura dell'ambiente, ad esempio gli host Docker, l'archiviazione, i controlli degli accessi basati sui ruoli (RBAC), la diagnostica e così via. Per comprendere meglio alcuni dei vantaggi, è possibile [leggere altre informazioni su Resource Manager](../resource-group-overview.md) e sui modelli. Il vantaggio dell'uso di modelli di Resource Manager rispetto al semplice impiego di Docker Machine è che è possibile definire altri controlli degli accessi, archiviazione, host di Docker e così via ed essere in grado di riprodurre le distribuzioni in base alle esigenze future.

## Distribuire un modello con l'estensione di VM Docker:

Per mostrare come distribuire una VM Ubuntu su cui è installata l'estensione di VM Docker, si userà un modello di avvio rapido esistente. Per visualizzare il modello, vedere [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) (Distribuzione semplice di una VM Ubuntu con Docker).

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

Al termine della distribuzione, stabilire una connessione SSH al nuovo host Docker con il nome DNS specificato durante la distribuzione. Gli strumenti Docker sono già installati, quindi si proverà a creare un contenitore nginx:

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

Aprire un Web browser e immettere il nome DNS specificato durante la distribuzione per visualizzare il contenitore in azione:

![Esecuzione di un contenitore ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Per altre informazioni sull'estensione della VM Docker, ad esempio la configurazione della porta TCP del daemon Docker, la configurazione della sicurezza e la distribuzione di contenitori con Docker Compose, vedere [Azure Virtual Machine Extension for Docker GitHub project](https://github.com/Azure/azure-docker-extension/) (Estensione della macchina virtuale di Azure per il progetto GitHub Docker).

## Riferimento al modello JSON per l'installazione dell'estensione di VM Docker

In questo esempio viene usato un modello di avvio rapido. È possibile usare i propri modelli di Resource Manager esistenti per installare l'estensione di VM Docker su macchine virtuali definite nel modello aggiungendo il codice seguente al file di definizione JSON:

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

Per altre procedure dettagliate relative all'uso di modelli di Resource Manager, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md).

## Passaggi successivi

Leggere passaggi più dettagliati per le diverse opzioni di distribuzione:

1. [Usare Docker Machine con il driver di Azure](./virtual-machines-linux-docker-machine.md)
2. [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](./virtual-machines-linux-classic-cli-use-docker.md)
3. [Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure](virtual-machines-linux-docker-compose-quickstart.md).
3. [Distribuire un cluster del servizio contenitore di Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0720_2016-->