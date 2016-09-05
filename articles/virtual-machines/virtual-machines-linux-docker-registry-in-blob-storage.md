<properties 
  pageTitle="Distribuzione del registro Docker privato in Azure | Microsoft Azure"
  description="Descrive come usare il registro Docker per ospitare immagini di contenitori nel servizio di archiviazione BLOB di Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="07/11/2016" 
  ms.author="ahmetb" />

# Distribuzione del registro Docker privato in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Questo documento spiega cos'è un registro Docker privato e mostra come distribuire un'immagine del contenitore Docker Registry 2.0 in un registro Docker privato in Microsoft Azure usando l'archiviazione BLOB di Azure.

Questo documento presuppone che:

1. Si sia in grado di usare Docker e si disponga di immagini Docker da archiviare. (No? [Informazioni su Docker](https://www.docker.com))
2. Si disponga di un server con il motore Docker installato. (No? [Esegui rapidamente l'installazione in Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## Che cos'è un registro Docker privato?

Per poter distribuire applicazioni di contenitori nel cloud, è necessario costruire un'immagine di contenitore Docker e archiviarla da qualche parte in modo che possa essere usata anche da altri utenti.

Mentre la creazione di un'immagine di contenitore e la relativa distribuzione nel cloud sono operazioni piuttosto semplici, archiviare l'immagine generata in modo affidabile può essere più difficile. Per questo motivo, Docker offre un servizio centralizzato denominato [Docker Hub][docker-hub] per l'archiviazione di immagini di contenitore nel cloud e consente di creare in qualsiasi momento nuovi contenitori con queste immagini.

Sebbene [Docker Hub][docker-hub] sia un servizio a pagamento per l'archiviazione di immagini di contenitori di applicazioni private, Docker rispetta le esigenze degli sviluppatori e offre un set di strumenti open-source per archiviare le immagini in un registro Docker privato, protetto da firewall o in locale, senza raggiungere la rete Internet pubblica. È possibile quindi sfruttare la semplicità di protezione dell'archiviazione BLOB di Azure per creare e usare in Azure un registro Docker privato, da gestire in modo autonomo.

## Perché ospitare un registro Docker in Azure?

L'hosting dell'istanza del registro Docker in Microsoft Azure e la memorizzazione di immagini nell'archiviazione BLOB di Azure offrono alcuni vantaggi:

**Sicurezza:** le immagini Docker non abbandonano mai i data center di Azure in modo che non debbano attraversare la rete Internet pubblica, proprio come se si usasse Docker Hub.
  
**Prestazioni:** le immagini Docker vengono archiviate nello stesso data center o nella stessa area geografica delle proprie applicazioni. Le immagini, in questo modo, vengono spostate in modo più veloce e affidabile rispetto a Docker Hub.

**Affidabilità:** usando l'archiviazione BLOB di Microsoft Azure si hanno a disposizione molte proprietà di archiviazione, tra cui disponibilità elevata, ridondanza, archiviazione Premium (unità SSD) e così via.

## Configurazione del registro Docker per usare l'archiviazione BLOB di Azure

(Si consiglia di leggere la [documentazione relativa a Docker Registry 2.0][registry-docs] prima di continuare.)

È possibile [configurare][registry-config] il registro Docker in due modi diversi. È possibile:

1. Usare un file `config.yml`. In questo caso, è necessario creare un'immagine Docker distinta basata sull'immagine `registry`.
2. Sostituire il file di configurazione predefinito tramite variabili di ambiente: consente di eseguire le operazioni senza creare e mantenere un'immagine Docker separata.

Per semplicità, questo argomento segue l'opzione 2 usando variabili di ambiente.

Per eseguire un'istanza del registro Docker che:

* usa l'account di Archiviazione di Azure per archiviare le immagini,
* è in ascolto sulla porta 5000 della macchina virtuale,
* non ha un'autenticazione configurata (scelta non consigliata, vedere la nota di seguito),

è necessario eseguire il seguente comando Docker nel terminale bash (sostituire `<storage-account>` e `<storage-key>` con le credenziali):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Dopo aver creato il comando, è possibile visualizzare il contenitore che ospita l'istanza del registro Docker privato eseguendo il comando `docker ps` sull'host:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] La procedura per configurare la protezione per il registro Docker non è illustrata in questo documento e, per impostazione predefinita, il registro sarà accessibile anche agli utenti senza autenticazione se si apre la porta per la porta del registro sull'endpoint della macchina virtuale o sul bilanciamento del carico se si usa il comando di distribuzione precedente.
>
> Leggere la [documentazione relativa alla configurazione del registro Docker][registry-config] per informazioni su come proteggere l'istanza del registro e le immagini.

## Passaggi successivi

Dopo aver configurato il registro, è possibile iniziare a usarlo. Iniziare con i documenti [registry-docs] del Docker.

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=AcomDC_0824_2016-->