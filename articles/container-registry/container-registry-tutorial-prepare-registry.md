---
title: Esercitazione per Registro contenitori di Azure - Preparare Registro contenitori di Azure con replica geografica
description: Creare un registro contenitori di Azure, configurare la replica geografica, preparare un'immagine Docker e distribuirla nel registro. Prima parte di una serie in tre parti.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7ae0fbf5f7566bd3f1f6591501b8b004a1e5cb0f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>Preparare un registro contenitori di Azure con replica geografica

Un registro contenitori di Azure è un registro Docker privato distribuito in Azure che è possibile mantenere in una posizione di rete vicina alle distribuzioni. In questa serie di tre esercitazioni vengono fornite istruzioni su come usare la replica geografica per distribuire un'applicazione Web ASP.NET Core in esecuzione in un contenitore Linux in due istanze di [app Web per i contenitori](../app-service/containers/index.yml). Verrà descritto come in Azure l'immagine viene distribuita automaticamente in ciascuna istanza dell'app Web dal repository con replica geografica più vicina.

In questa esercitazione, che è la prima di una serie in tre parti, viene descritto come eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare un registro contenitori di Azure con replica geografica
> * Clonare il codice sorgente dell'applicazione da GitHub
> * Compilare un'immagine del contenitore Docker dall'origine applicazione
> * Eseguire il push dell'immagine del contenitore nel registro

Nelle esercitazioni successive si distribuirà il contenitore dal registro privato a un'app Web in esecuzione in due aree di Azure. Si aggiornerà quindi il codice nell'applicazione e verranno infine aggiornate entrambe le istanze dell'app Web con un singolo `docker push` nel registro.

## <a name="before-you-begin"></a>Prima di iniziare

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali. Se necessario, vedere [Introduzione a Docker]( https://docs.docker.com/get-started/) per una panoramica sui concetti fondamentali relativi al contenitore.

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È quindi consigliabile un'installazione locale dell'ambiente di sviluppo dell'interfaccia della riga di comando di Azure e di Docker.

> [!IMPORTANT]
> La funzionalità di replica geografica del servizio Registro contenitori di Azure è attualmente in versione di **anteprima**. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

Accedere al [portale di Azure](http://portal.azure.com).

Selezionare **Nuovo** > **Contenitori** > **Registro contenitori di Azure**.

![Creazione di un registro contenitori con il portale di Azure][tut-portal-01]

Configurare il nuovo registro con le impostazioni seguenti:

* **Nome registro**: creare un nome di registro univoco a livello globale all'interno di Azure e contenente da 5 a 50 caratteri alfanumerici
* **Gruppo di risorse**: **Crea nuovo** > `myResourceGroup`
* **Percorso**: `West US`
* **Utente amministratore**: `Enable` (obbligatorio per app Web per contenitori per eseguire il pull delle immagini)
* **SKU**: `Premium` (obbligatorio per la replica geografica)

Selezionare **Crea** per distribuire l'istanza del record di controllo di accesso.

![Creazione di un registro contenitori con il portale di Azure][tut-portal-02]

Nella parte restante di questa esercitazione si usa `<acrName>` come segnaposto per il **nome del registro** contenitori scelto.

> [!TIP]
> Poiché i Registri contenitori di Azure sono in genere risorse di lunga durata usate tra più host del contenitore, è consigliabile creare il registro in un gruppo di risorse specifico. Durante la configurazione di registri con replica geografica e webhook queste risorse aggiuntive vengono inserite nello stesso gruppo di risorse.
>

## <a name="configure-geo-replication"></a>Configurare la replica geografica

Dopo aver creato un registro Premium, è possibile configurare la replica geografica. L'app web, che nella prossima esercitazione viene configurata per l'esecuzione in due aree, possono quindi eseguire il pull delle relative immagini del contenitore dal registro più vicino.

Passare al nuovo registro contenitori nel portale di Azure e selezionare **Repliche** in **Servizi**:

![Repliche nell'interfaccia utente del registro contenitori del portale di Azure][tut-portal-03]

Viene visualizzata una mappa che mostra esagoni verdi a indicare le aree di Azure disponibili per la replica geografica:

 ![Mappa delle aree nel portale di Azure][tut-map-01]

Replicare il registro per l'area Stati Uniti orientali selezionando il relativo esagono verde e quindi selezionare **Crea** in **Crea replica**:

 ![Interfaccia utente Crea replica nel portale di Azure][tut-portal-04]

Dopo aver completato la replica, nel portale viene visualizzato *Pronta* per entrambe le aree. Usare il pulsante **Aggiorna** per aggiornare lo stato della replica. La creazione o la sincronizzazione delle repliche può richiedere un minuto circa.

![Interfaccia utente Stato replica nel portale di Azure][tut-portal-05]

## <a name="container-registry-login"></a>Accesso al registro contenitori

Dopo aver configurato la replica geografica, generare un'immagine del contenitore ed eseguirne il push nel registro. È necessario accedere all'istanza di Registro contenitori di Azure prima di eseguirvi il push delle immagini. Con gli [SKU Basic, Standard e Premium](container-registry-skus.md) è possibile eseguire l'autenticazione usando l'identità di Azure.

Usare il comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) per autenticare e memorizzare nella cache le credenziali del registro. Sostituire `<acrName>` con il nome del registro creato nei passaggi precedenti.

```azurecli
az acr login --name <acrName>
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'esempio in questa esercitazione include una semplice applicazione Web compilata con [ASP.NET Core](http://dot.net). L'app usa una pagina HTML che visualizza l'area da cui l'immagine viene distribuita dal Registro contenitori di Azure.

![App dell'esercitazione visualizzata in un browser][tut-app-01]

Usare git per scaricare l'esempio in una directory locale ed eseguire il comando `cd` nella directory:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Aggiornare il documento Dockerfile

Il documento Dockerfile fornito nell'esempio illustra come viene compilato il contenitore. Questo documento viene creato da un'immagine [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) ufficiale, copia i file dell'applicazione nel contenitore, installa le dipendenze, compila l'output usando l'immagine [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build) ufficiale e quindi compila un'immagine aspnetcore ottimizzata.

Il documento Dockerfile si trova nella directory `./AcrHelloworld/Dockerfile` dell'origine clonata.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

L'applicazione nell'immagine *acr helloworld* tenta di determinare l'area da cui il contenitore è stato distribuito eseguendo una query DNS sulle informazioni relative al server di accesso del registro. È necessario specificare l'URL del server di accesso del registro nella variabile di ambiente `DOCKER_REGISTRY` del documento Dockerfile.

Recuperare prima l'URL del server di accesso del registro con il comando `az acr show`. Sostituire `<acrName>` con il nome del registro creato nei passaggi precedenti.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Output:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Aggiornare quindi la riga `DOCKER_REGISTRY` con l'URL del server di accesso del registro. In questo esempio si aggiorna la riga in base al nome del registro usato nell'esempio, ovvero *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Compilare l'immagine del contenitore

Dopo aver aggiornato il documento Dockerfile con l'URL del registro, è possibile usare il comando `docker build` per creare l'immagine del contenitore. Eseguire il comando seguente per compilare l'immagine e contrassegnarla con l'URL del registro privato, sostituendo nuovamente `<acrName>` con il nome del registro in uso:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Durante la compilazione dell'immagine Docker vengono visualizzate più righe di output (troncate in questo esempio):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Usare il comando `docker images` per visualizzare l'immagine compilata:

```bash
docker images
```

Output:

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Eseguire il push dell'immagine in Registro contenitori di Azure

Usare infine il comando `docker push` per eseguire il push dell'immagine *acr-helloworld* al registro. Sostituire `<acrName>` con il nome del registro.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Poiché il registro è stato configurato per la replica geografica, l'immagine viene replicata automaticamente sia nell'area *Stati Uniti occidentali* che nell'area *Stati Uniti orientali* con questo singolo comando `docker push`.

Output:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un registro contenitori privato con replica geografica, è stata compilata un'immagine del contenitore e quindi è stato eseguito il push di tale immagine nel registro. L'esecuzione dei passaggi descritti in questa esercitazione sono state eseguite le attività seguenti:

> [!div class="checklist"]
> * Creazione di un registro contenitori di Azure con replica geografica
> * Clonazione del codice sorgente dell'applicazione da GitHub
> * Compilazione di un'immagine del contenitore Docker dall'origine applicazione
> * Esecuzione del push dell'immagine del contenitore nel registro

Passare all'esercitazione successiva per altre informazioni sulla distribuzione del contenitore in più istanze dell'app Web per contenitori usando la replica geografica per gestire le immagini in locale.

> [!div class="nextstepaction"]
> [Distribuire un'app Web dal Registro contenitori di Azure](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
