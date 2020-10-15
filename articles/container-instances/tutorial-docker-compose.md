---
title: Esercitazione - Usare Docker Compose per distribuire un gruppo multi-contenitore
description: Usare Docker Compose per compilare ed eseguire un'applicazione multi-contenitore e quindi visualizzare l'applicazione in Istanze di Azure Container
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: ''
ms.openlocfilehash: 1e8a5cd856358a0dc3e9c356cb3a55f75db29c86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708273"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Esercitazione: Distribuire un gruppo multi-contenitore usando Docker Compose 

In questa esercitazione si usa [Docker Compose](https://docs.docker.com/compose/) per definire ed eseguire un'applicazione multi-contenitore in locale e quindi distribuirla come [gruppo di contenitori](container-instances-container-groups.md) in Istanze di Azure Container. 

Eseguire i contenitori in Istanze di Azure Container su richiesta quando si sviluppano app native del cloud con Docker e si vuole passare facilmente dallo sviluppo locale alla distribuzione cloud. Questa funzionalità è abilitata dall'[integrazione tra Docker e Azure](https://docs.docker.com/engine/context/aci-integration/). È possibile usare i comandi di Docker nativi per eseguire [una singola istanza di contenitore](quickstart-docker-cli.md) o un gruppo multi-contenitore in Azure.

> [!IMPORTANT]
> Non sono supportate tutte le funzionalità di Istanze di Azure Container. È possibile fornire feedback sull'integrazione fra Docker e Azure creando un problema nel repository di GitHub [Docker ACI Integration](https://github.com/docker/aci-integration-beta).

> [!TIP]
> È possibile usare l'[estensione Docker per Visual Studio Code](https://aka.ms/VSCodeDocker) per un'esperienza integrata di sviluppo, esecuzione e gestione di contenitori, immagini e contesti.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Creare un Registro Azure Container
> * Clonare il codice sorgente dell'applicazione da GitHub
> * Usare Docker Compose per compilare un'immagine ed eseguire un'applicazione multi-contenitore in locale
> * Eseguire il push dell'immagine dell'applicazione nel registro contenitori
> * Creare un contesto di Azure per Docker
> * Visualizzare l'applicazione in Istanze di Azure Container

## <a name="prerequisites"></a>Prerequisiti

* **Interfaccia della riga di comando di Azure**: è necessario che nel computer locale sia installata l'interfaccia della riga di comando di Azure. È consigliabile usare la versione 2.10.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

* **Docker Desktop**: è necessario usare Docker Desktop versione 2.3.0.5 o successiva, disponibile per [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) o [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) oppure installare l'[interfaccia della riga di comando di integrazione fra Docker e Istanze di Azure Container per Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'applicazione di esempio utilizzata in questa esercitazione è un'app di voto base. L'applicazione è costituita da un componente Web front-end e un'istanza Redis back-end. Viene creato un pacchetto del componente Web in un'immagine del contenitore personalizzata. L'istanza di Redis usa un'immagine non modificata dell'hub Docker.

Usare [git](https://git-scm.com/downloads) per clonare l'applicazione di esempio nell'ambiente di sviluppo:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Passare alla directory clonata.

```console
cd azure-voting-app-redis
```

All'interno della directory sono disponibili il codice sorgente dell'applicazione e un file di Docker Compose creato in precedenza, docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Modificare il file di Docker Compose

Aprire docker-compose.yaml in un editor di testo. Il file configura i servizi `azure-vote-back` e `azure-vote-front`.

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

Nella configurazione di `azure-vote-front`, apportare le due modifiche seguenti:

1. Aggiornare la proprietà `image` nel servizio `azure-vote-front`. Aggiungere al nome dell'immagine un prefisso costituito dal nome del server di accesso del registro contenitori di Azure, \<acrName\>.azurecr.io. Se ad esempio il registro è denominato *myregistry*, il nome del server di accesso è *myregistry.azurecr.io* (tutto minuscolo) e la proprietà image è quindi `myregistry.azurecr.io/azure-vote-front`.
1. Impostare il mapping di `ports` su `80:80`. Salvare il file.

Il file aggiornato dovrebbe essere simile al seguente:

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Eseguendo queste sostituzioni, l'immagine di `azure-vote-front` compilata nel passaggio successivo viene contrassegnata per il registro contenitori di Azure ed è possibile eseguire il pull dell'immagine per l'esecuzione in Istanze di Azure Container.

> [!TIP]
> Non è necessario usare un registro contenitori di Azure per questo scenario. È ad esempio possibile scegliere un repository privato in Docker Hub in cui ospitare l'immagine dell'applicazione. Se si sceglie un registro diverso, aggiornare in modo appropriato la proprietà image.

## <a name="run-multi-container-application-locally"></a>Eseguire l'applicazione multi-contenitore in locale

Eseguire [docker-compose up](https://docs.docker.com/compose/reference/up/), che usa il file `docker-compose.yaml` di esempio per creare l'immagine del contenitore, scaricare l'immagine Redis e avviare l'applicazione:

```console
docker-compose up --build -d
```

Al termine usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per vedere le immagini create. Sono state scaricate o create tre immagini. L'immagine `azure-vote-front` contiene l'applicazione front-end, che usa l'immagine `uwsgi-nginx-flask` come base. L'immagine `redis` viene usata per avviare un'istanza di Redis.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
redis                                     latest     a1b99da73d05        7 days ago          104MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Eseguire il comando [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) per vedere i contenitori in esecuzione:

```
$ docker ps

CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front  "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b68fed4b66b6        redis                                   "docker-entrypoint.s…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Per vedere l'applicazione in esecuzione, immettere `http://localhost:80` in un Web browser locale. L'applicazione di esempio viene caricata, come illustrato nell'esempio seguente:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Immagine dell'app di voto":::

Dopo aver provato l'applicazione locale, eseguire [docker-compose down](https://docs.docker.com/compose/reference/down/) per arrestare l'applicazione e rimuovere i contenitori.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Eseguire il push dell'immagine nel registro contenitori

Per distribuire l'applicazione in Istanze di Azure Container, è necessario eseguire il push dell'immagine `azure-vote-front` nel registro contenitori. Eseguire [docker-compose push](https://docs.docker.com/compose/reference/push) per eseguire il push dell'immagine:

```console
docker-compose push
```

Il push nel registro può richiedere alcuni minuti.

Per verificare che l'immagine sia stata archiviata nel registro, eseguire il comando [az acr repository show](/cli/azure/acr/repository#az-acr-repository-show):

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Distribuire l'applicazione in Istanze di Azure Container

Successivamente passare al contesto di Istanze di Azure Container. I comandi di Docker successivi vengono eseguiti in questo contesto.

```console
docker context use myacicontext
```

Eseguire `docker compose up` per avviare l'applicazione in Istanze di Azure Container. Viene eseguito il pull dell'immagine `azure-vote-front` dal registro contenitori e viene creato il gruppo di contenitori in Istanze di Azure Container.

```console
docker compose up
```

> [!NOTE]
> I comandi di Docker Compose attualmente disponibili in un contesto di Istanze di Azure Container sono `docker compose up` e `docker compose down`. In questi comandi non sono presenti trattini tra `docker` e `compose`.

Il gruppo di contenitori viene distribuito rapidamente. Output di esempio:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Eseguire `docker ps` per visualizzare i contenitori in esecuzione e l'indirizzo IP assegnato al gruppo di contenitori.

```console
docker ps
```

Output di esempio:

```
CONTAINER ID                           IMAGE                                    COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    redis                                                        Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                       Running             52.179.23.131:80->80/tcp
```

Per vedere l'applicazione in esecuzione nel cloud, immettere l'indirizzo IP visualizzato in un Web browser locale. In questo esempio immettere `52.179.23.131`. L'applicazione di esempio viene caricata, come illustrato nell'esempio seguente:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Immagine dell'app di voto":::

Per visualizzare i log del contenitore front-end, eseguire il comando [docker logs](https://docs.docker.com/engine/reference/commandline/logs). Esempio:

```console
docker logs azurevotingappredis_azure-vote-front
```

È anche possibile usare il portale di Azure o altri strumenti di Azure per visualizzare le proprietà e lo stato del gruppo di contenitori distribuito.

Dopo aver terminato di provare l'applicazione, arrestare l'applicazione e i contenitori con `docker compose down`:

```console
docker compose down
```

Questo comando elimina il gruppo di contenitori in Istanze di Azure Container.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è usato Docker Compose per passare dall'esecuzione di un'applicazione multi-contenitore in locale all'esecuzione in Istanze di Azure Container. Si è appreso come:

> [!div class="checklist"]
> * Creare un Registro Azure Container
> * Clonare il codice sorgente dell'applicazione da GitHub
> * Usare Docker Compose per compilare un'immagine ed eseguire un'applicazione multi-contenitore in locale
> * Eseguire il push dell'immagine dell'applicazione nel registro contenitori
> * Creare un contesto di Azure per Docker
> * Visualizzare l'applicazione in Istanze di Azure Container

È anche possibile usare l'[estensione Docker per Visual Studio Code](https://aka.ms/VSCodeDocker) per un'esperienza integrata di sviluppo, esecuzione e gestione di contenitori, immagini e contesti.

Per sfruttare i vantaggi di altre funzionalità in Istanze di Azure Container, usare gli strumenti di Azure per specificare un gruppo multi-contenitore. Vedere, ad esempio, le esercitazioni per distribuire un gruppo di contenitori usando l'interfaccia della riga di comando di Azure con un [file YAML](container-instances-multi-container-yaml.md) o per eseguire la distribuzione usando un [modello di Azure Resource Manager](container-instances-multi-container-group.md). 