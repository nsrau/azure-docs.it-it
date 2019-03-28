---
title: Creare funzioni di Azure in Linux tramite un'immagine personalizzata
description: Informazioni su come creare Funzioni di Azure in esecuzione in un'immagine personalizzata di Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 02/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 92811110ef44676de487bca1ad2022cb63315c75
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418058"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Creare una funzione in Linux tramite un'immagine personalizzata

Funzioni di Azure consente di ospitare le funzioni in Linux in un contenitore personalizzato. È anche possibile [ospitare in un contenitore di Servizio app di Azure predefinito](functions-create-first-azure-function-azure-cli-linux.md). Questa funzionalità richiede [il runtime di Funzioni 2.x](functions-versions.md).

In questa esercitazione si vedrà come distribuire funzioni in Azure come un'immagine personalizzata di Docker. Questo modello è utile quando è necessario personalizzare l'immagine del contenitore del servizio app predefinita. È possibile che l'utente desideri usare un'immagine personalizzata quando le funzioni hanno bisogno di una versione di linguaggio specifica o richiedono una configurazione o una dipendenza specifica non indicata all'interno dell'immagine predefinita. Le immagini di base supportate per Funzioni di Azure sono disponibili nel [repository di immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base). Il [supporto di Python](functions-reference-python.md) attualmente è disponibile in anteprima.

Questa esercitazione illustra come usare Azure Functions Core Tools per creare una funzione in un'immagine Linux personalizzata. L'immagine viene pubblicata in Azure, in un'app per le funzioni creata tramite l'interfaccia della riga di comando di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per le funzioni e un Dockerfile usando Core Tools.
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori.
> * Creare un account di archiviazione di Azure.
> * Creare un piano di servizio app Linux.
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.
> * adsa

I passaggi seguenti sono supportati in computer Mac, Windows o Linux.  

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

* Installare la [versione 2.x di Core Tools di Azure](functions-run-local.md#v2).

* Installare l'[interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso.  
È possibile usare anche [Azure Cloud Shell](https://shell.azure.com/bash).

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Creare un progetto di app per la funzione locale

Eseguire il comando seguente dalla riga di comando per creare un progetto di app della funzione nella cartella `MyFunctionProj` della directory locale corrente.

```bash
func init MyFunctionProj --docker
```

Quando si include l'opzione `--docker`, viene generato un Dockerfile per il progetto. Questo file viene usato per creare un contenitore personalizzato in cui eseguire il progetto. L'immagine di base usata dipende dal linguaggio del runtime del ruolo di lavoro scelto.  

Quando richiesto, scegliere un runtime del ruolo di lavoro tra i linguaggi seguenti:

* `dotnet`: crea un progetto libreria di classi .NET (.csproj).
* `node`: crea un progetto JavaScript.
* `python`: crea un progetto Python.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Quando viene eseguito il comando, viene visualizzato qualcosa di simile a quanto segue:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Usare il comando seguente per passare alla nuova cartella del progetto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Creare l'immagine dal file di Docker

Esaminiamo il _Dockerfile_ nella cartella radice del progetto. Il file descrive l'ambiente necessario per eseguire l'app per le funzioni su Linux. L'esempio seguente mostra un Dockerfile che crea un contenitore che esegue un'app per le funzioni nel runtime del ruolo di lavoro JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> L'elenco completo di immagini di base supportate per Funzioni di Azure è disponibile nella [pagina delle immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Eseguire il comando `build`
Nella cartella radice eseguire il comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e specificare il nome `mydockerimage` e il tag `v1.0.0`. Sostituire `<docker-id>` con l'ID dell'account Docker Hub. Questo comando compila l'immagine Docker per il contenitore.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Quando il comando viene eseguito, viene visualizzato un output simile al seguente, che in questo caso è relativo a un runtime del ruolo di lavoro JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testare l'immagine in locale
Verificare che l'immagine creata funzioni eseguendo l'immagine Docker in un contenitore locale. Eseguire il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e passargli il nome e il tag dell'immagine. Accertarsi di specificare la porta usando l'argomento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Quando l'immagine personalizzata è in esecuzione in un contenitore Docker locale, verificare che l'app per le funzioni e il contenitore funzionino correttamente passando a <http://localhost:8080>.

![Testare l'app per le funzioni in locale.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Facoltativamente, è possibile eseguire un nuovo test della funzione, questa volta nel contenitore locale usando l'URL seguente:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Dopo aver verificato l'app per le funzioni nel contenitore, arrestare l'esecuzione. A questo punto, è possibile propagare l'immagine personalizzata nel proprio account dell'hub Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Eseguire il push dell'immagine personalizzata nell'hub Docker

Un registro è un'applicazione che ospita le immagini e fornisce l'immagine dei servizi e i servizi contenitore. Per condividere l'immagine, è necessario eseguirne il push in un registro. Docker Hub è un registro per le immagini Docker che consente di ospitare i propri repository, pubblici o privati.

Prima di eseguire il push di un'immagine, è necessario accedere ad hub Docker usando il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Sostituire `<docker-id>` con il nome dell'account e digitare la password nella console quando richiesto. Per altre opzioni relative alla password di hub Docker, vedere la [documentazione relativa al comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito. Dopo avere effettuato l'accesso, eseguire il push dell'immagine nell'hub Docker usando il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verificare che il push sia riuscito esaminando l'output del comando.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

A questo punto è possibile usare questa immagine come origine di distribuzione per una nuova app per le funzioni in Linux.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creare un nuovo piano di servizio app Linux

L'hosting di Funzioni in Linux non è attualmente supportato per i piani di consumo. Per l'hosting delle app contenitore Linux è necessario un piano di servizio app Linux. Per altre informazioni sull'hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Creare e distribuire l'immagine personalizzata

L'app per le funzioni ospita l'esecuzione delle funzioni. Creare un'app per le funzioni da un'immagine dell'hub Docker usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

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

Il parametro _deployment-container-image-name_ indica l'immagine ospitata nell'hub Docker da usare per creare l'app per le funzioni. Usare il comando [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) per visualizzare informazioni sull'immagine usata per la distribuzione. Usare il comando [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) per distribuire da un'immagine diversa.

## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

La funzione richiede la stringa di connessione per connettersi all'account di archiviazione predefinito. Quando si pubblica l'immagine personalizzata in un account di un contenitore privato, è invece necessario definire le impostazioni dell'applicazione come variabili di ambiente in Dockerfile, ad esempio usando l'[istruzione ENV](https://docs.docker.com/engine/reference/builder/#env).

In questo caso `<storage_name>` è il nome dell'account di archiviazione creato. Ottenere la stringa di connessione con il comando [az storage account show-connection-string](/cli/azure/storage/account). Aggiungere le impostazioni dell'applicazione nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Se il contenitore è privato, è necessario impostare anche le seguenti impostazioni dell'applicazione  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> È necessario arrestare e avviare l'app per le funzioni affinché vengano rilevati questi valori

È ora possibile testare le funzioni in esecuzione su Linux in Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>adsa

Uno dei vantaggi dell'uso dei contenitori è la capacità di distribuire automaticamente gli aggiornamenti quando i contenitori vengono aggiornati nel registro. Abilitare la distribuzione continua con il comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config).

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Questo comando restituisce l'URL del webhook di distribuzione dopo l'abilitazione della distribuzione continua. È anche possibile usare il comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) per ottenere questo URL. 

Copiare l'URL di distribuzione e passare al repository DockerHub, scegliere la scheda **Webhook**, digitare il **Nome webhook**, incollare l'URL in **URL webhook**e quindi scegliere il segno più (**+**).

![Aggiungere il webhook nel repository DockerHub](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Con il webhook configurato, ogni volta che l'immagine collegata in DockerHub viene aggiornata l'app per le funzioni scarica e installa l'immagine.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un'app per le funzioni e un Dockerfile usando Core Tools.
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori.
> * Creare un account di archiviazione di Azure.
> * Creare un piano di servizio app Linux.
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.

Informazioni su come abilitare la funzionalità di integrazione continua inclusa nella piattaforma del servizio app di base. È possibile configurare l'app per le funzioni in modo che il contenitore venga ridistribuito quando si aggiorna l'immagine nell'hub Docker.

> [!div class="nextstepaction"] 
> [Continuous deployment with Web App for Containers](../app-service/containers/app-service-linux-ci-cd.md) (Distribuzione continua con l'app Web per contenitori)
