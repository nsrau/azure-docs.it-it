---
title: Creare una funzione in Linux tramite un'immagine personalizzata (anteprima) | Microsoft Docs
description: Informazioni su come creare Funzioni di Azure in esecuzione in un'immagine personalizzata di Linux.
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 9ba5f45034561f8d897676e8cc4b1a59945403b8
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Creare una funzione in Linux tramite un'immagine personalizzata (anteprima)

Funzioni di Azure consente di ospitare le funzioni in Linux in un contenitore personalizzato. Questa funzionalità è attualmente disponibile in anteprima. È anche possibile [ospitare in un contenitore di Servizio app di Azure predefinito](functions-create-first-azure-function-azure-cli-linux.md).  

In questa esercitazione si imparerà come distribuire un'app per le funzioni come un'immagine personalizzata di Docker. Questo modello è utile quando è necessario personalizzare l'immagine del contenitore del servizio app predefinita. È possibile che l'utente desideri usare un'immagine personalizzata quando le funzioni hanno bisogno di una versione di linguaggio specifica o richiedono una configurazione o una dipendenza specifica non indicata all'interno dell'immagine predefinita.

Questa esercitazione illustra come usare Funzioni di Azure per creare ed eseguire il push di un'immagine personalizzata in un hub Docker. Usare quindi questa immagine come origine di distribuzione per un'app per le funzioni che viene eseguita su Linux. Usare Docker per compilare ed eseguire il push dell'immagine. Usare l'interfaccia della riga di comando di Azure per creare un'app per le funzioni e distribuire l'immagine dall'hub Docker. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori. 
> * Creare un account di archiviazione di Azure. 
> * Creare un piano di servizio app Linux. 
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni. 

I passaggi seguenti sono supportati in computer Mac, Windows o Linux.  

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* [Git](https://git-scm.com/downloads)
* Una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) attiva
* [Docker](https://docs.docker.com/get-started/#setup)
* Un [account Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire il comando seguente per clonare il repository di app di esempio nel computer locale, quindi passare alla directory che contiene il codice di esempio.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Creare l'immagine dal file di Docker

Nel repository Git esaminare _Dockerfile_. Il file descrive l'ambiente necessario per eseguire l'app per le funzioni su Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Durante l'hosting di un'immagine in un registro contenitori privato, è necessario aggiungere le impostazioni di connessione all'app per le funzioni usando le variabili **ENV** in Dockerfile. Poiché questa esercitazione non garantisce l'uso di un registro privato, le impostazioni di connessione vengono [aggiunte in seguito alla distribuzione tramite l'interfaccia della riga di comando di Azure](#configure-the-function-app) come procedura consigliata di sicurezza.   

### <a name="run-the-build-command"></a>Eseguire il comando Build
Per creare l'immagine Docker, eseguire il comando `docker build` e specificare il nome `mydockerimage` e il tag `v1.0.0`. Sostituire `<docker-id>` con l'ID dell'account Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

L'output generato dal comando sarà simile al seguente:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testare l'immagine in locale
Verificare che l'immagine creata funzioni eseguendo l'immagine Docker in un contenitore locale. Eseguire il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e passargli il nome e il tag dell'immagine. Accertarsi di specificare la porta usando l'argomento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Quando l'immagine personalizzata è in esecuzione in un contenitore Docker locale, verificare che l'app per le funzioni e il contenitore funzionino correttamente passando a <http://localhost:8080>.

![Testare l'app per le funzioni in locale.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Dopo aver verificato l'app per le funzioni nel contenitore, arrestare l'esecuzione. A questo punto, è possibile propagare l'immagine personalizzata nel proprio account dell'hub Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Eseguire il push dell'immagine personalizzata nell'hub Docker

Un registro è un'applicazione che ospita le immagini e fornisce l'immagine dei servizi e i servizi contenitore. Per condividere l'immagine, è necessario eseguirne il push in un registro. Docker Hub è un registro per le immagini Docker che consente di ospitare i propri repository, pubblici o privati. 

Prima di eseguire il push di un'immagine, è necessario accedere ad hub Docker usando il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Sostituire `<docker-id>` con il nome dell'account e digitare la password nella console quando richiesto. Per altre opzioni relative alla password di hub Docker, vedere la [documentazione relativa al comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito. Dopo avere effettuato l'accesso, eseguire il push dell'immagine nell'hub Docker usando il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Verificare che il push sia riuscito esaminando l'output del comando.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
A questo punto è possibile usare questa immagine come origine di distribuzione per una nuova app per le funzioni in Linux. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessaria la versione 2.0.21 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creare un nuovo piano di servizio app Linux

L'hosting di Funzioni in Linux non è attualmente supportato per i piani di consumo. È necessario avviare l'esecuzione su un piano di servizio app Linux. Per altre informazioni sull'hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Creare e distribuire l'immagine personalizzata

L'app per le funzioni ospita l'esecuzione delle funzioni. Creare un'app per le funzioni da un'immagine dell'hub Docker usando il comando [az functionapp create](/cli/azure/functionapp#create). 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. Come in precedenza, `<docker-id>` è il nome dell'account Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
Al termine della creazione dell'app per le funzioni, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Il parametro _deployment-container-image-name_ indica l'immagine ospitata nell'hub Docker da usare per creare l'app per le funzioni. 


## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

La funzione richiede la stringa di connessione per connettersi all'account di archiviazione predefinito. Quando si pubblica l'immagine personalizzata in un account di un contenitore privato, è invece necessario impostare le impostazioni dell'applicazione come variabili di ambiente in Dockerfile usando l'[istruzione ENV](https://docs.docker.com/engine/reference/builder/#env) o un'istruzione equivalente. 

In questo caso `<storage_account>` è il nome dell'account di archiviazione creato. Ottenere la stringa di connessione con il comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Aggiungere le impostazioni dell'applicazione nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

È ora possibile testare le funzioni in esecuzione su Linux in Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori. 
> * Creare un account di archiviazione di Azure. 
> * Creare un piano di servizio app Linux. 
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.

Altre informazioni sullo sviluppo locale di Funzioni di Azure mediante gli strumenti di base di Funzioni di Azure.

> [!div class="nextstepaction"] 
> [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md)
