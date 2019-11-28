---
title: Creare funzioni di Azure in Linux tramite un'immagine personalizzata
description: Informazioni su come creare Funzioni di Azure in esecuzione in un'immagine personalizzata di Linux.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 7bf079f84978539735f3bbf5bb13b18130871fb1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484389"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Creare una funzione in Linux tramite un'immagine personalizzata

Funzioni di Azure consente di ospitare le funzioni in Linux in un contenitore personalizzato. È anche possibile [ospitare in un contenitore di Servizio app di Azure predefinito](functions-create-first-azure-function-azure-cli-linux.md). Questa funzionalità richiede [il runtime di Funzioni 2.x](functions-versions.md).

In questa esercitazione si vedrà come distribuire funzioni in Azure come un'immagine personalizzata di Docker. Questo modello è utile quando è necessario personalizzare l'immagine del contenitore predefinita. È possibile che l'utente desideri usare un'immagine personalizzata quando le funzioni hanno bisogno di una versione di linguaggio specifica o richiedono una configurazione o una dipendenza specifica non indicata all'interno dell'immagine predefinita. Le immagini di base supportate per Funzioni di Azure sono disponibili nel [repository di immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base). 

Questa esercitazione illustra come usare Azure Functions Core Tools per creare una funzione in un'immagine Linux personalizzata. L'immagine viene pubblicata in Azure, in un'app per le funzioni creata tramite l'interfaccia della riga di comando di Azure. Successivamente, si aggiorna la funzione per connettersi all'archiviazione code di Azure. Si abilita inoltre la distribuzione continua.  

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per le funzioni e un Dockerfile usando Core Tools.
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori.
> * Creare un account di archiviazione di Azure.
> * Creare un piano di hosting Premium.
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.
> * Abilitare la distribuzione continua.
> * Abilitare le connessioni SSH al contenitore.
> * Aggiungere un'associazione di output dell'archiviazione code. 

I passaggi seguenti sono supportati in computer Mac, Windows o Linux. 

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

* Installare la [versione 2.x di Core Tools di Azure](functions-run-local.md#v2).

* Installare l'[interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso.  
È possibile usare anche [Azure Cloud Shell](https://shell.azure.com/bash).

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Creare il progetto locale

Eseguire il comando seguente dalla riga di comando per creare un progetto di app della funzione nella cartella `MyFunctionProj` della directory locale corrente. Per un progetto Python, è [necessario usare un ambiente virtuale](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

Quando si include l'opzione `--docker`, viene generato un Dockerfile per il progetto. Questo file viene usato per creare un contenitore personalizzato in cui eseguire il progetto. L'immagine di base usata dipende dal linguaggio del runtime del ruolo di lavoro scelto.  

Quando richiesto, scegliere un runtime del ruolo di lavoro tra i linguaggi seguenti:

* `dotnet`: crea un progetto libreria di classi .NET (.csproj).
* `node`: crea un progetto JavaScript.
* `python`: crea un progetto Python.  

Usare il comando seguente per passare alla nuova cartella del progetto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Creare dal file Docker

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

Al termine del comando, è possibile eseguire il nuovo contenitore in locale.

### <a name="run-the-image-locally"></a>Creare l'immagine in locale
Verificare che l'immagine creata funzioni eseguendo l'immagine Docker in un contenitore locale. Eseguire il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e passargli il nome e il tag dell'immagine. Accertarsi di specificare la porta usando l'argomento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Quando l'immagine personalizzata è in esecuzione in un contenitore Docker locale, verificare che l'app per le funzioni e il contenitore funzionino correttamente passando a <http://localhost:8080>.

![Eseguire l'app per le funzioni in locale.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> A questo punto, quando si tenta di chiamare la funzione HTTP specifica, si ottiene un errore HTTP 401 come risposta. Questo accade perché la funzione viene eseguita nel contenitore locale come avverrebbe in Azure, il che significa che è necessario il tasto funzione. Poiché il contenitore non è stato ancora pubblicato in un'app per le funzioni, non è disponibile alcun tasto funzione. In un secondo momento, quando si ricorrerà a Core Tools per pubblicare il contenitore, i tasti funzione verranno visualizzati. Se si vuole testare la funzione in esecuzione nel contenitore locale, è possibile impostare la [chiave di autorizzazione](functions-bindings-http-webhook.md#authorization-keys) su `anonymous`. 

Dopo aver verificato l'app per le funzioni nel contenitore, arrestare l'esecuzione. A questo punto, è possibile propagare l'immagine personalizzata nel proprio account dell'hub Docker.

## <a name="push-to-docker-hub"></a>Eseguire il push in Docker Hub

Un registro è un'applicazione che ospita le immagini e fornisce l'immagine dei servizi e i servizi contenitore. Per condividere l'immagine, è necessario eseguirne il push in un registro. Docker Hub è un registro per le immagini Docker che consente di ospitare i propri repository, pubblici o privati.

Prima di eseguire il push di un'immagine, è necessario accedere ad hub Docker usando il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Sostituire `<docker-id>` con il nome dell'account e digitare la password nella console quando richiesto. Per altre opzioni relative alla password di hub Docker, vedere la [documentazione relativa al comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito. Dopo avere effettuato l'accesso, eseguire il push dell'immagine nell'hub Docker usando il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Al termine del push è possibile usare questa immagine come origine di distribuzione per una nuova app per le funzioni in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Creare un piano Premium

L'hosting Linux per i contenitori di funzioni personalizzate è supportato nei [piani dedicati (Servizio app)](functions-scale.md#app-service-plan) e nei [piani Premium](functions-premium-plan.md#features). In questa esercitazione si usa un piano Premium, che può essere ridimensionato in base alle esigenze. Per altre informazioni sull'hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md).

L'esempio seguente crea un piano Premium denominato `myPremiumPlan` nel piano tariffario **Elastico Premium 1** (`--sku EP1`), nell'area Stati Uniti occidentali (`-location WestUS`) e in un contenitore Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Creare un'app dall'immagine

Un'app per le funzioni gestisce l'esecuzione delle funzioni nel piano di hosting. Creare un'app per le funzioni da un'immagine dell'hub Docker usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. Come in precedenza, `<docker-id>` è il nome dell'account Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
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

## <a name="verify-your-functions"></a>Verificare le funzioni

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

La funzione attivata da HTTP creata richiede una [chiave di funzione](functions-bindings-http-webhook.md#authorization-keys) per chiamare l'endpoint. A questo punto, il modo più semplice per ottenere l'URL della funzione, inclusa la chiave, è dal [portale di Azure]. 

> [!TIP]
> È anche possibile ottenere le chiavi di funzione usando le [API di gestione delle chiavi](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), che richiedono la presentazione di un [token di connessione per l'autenticazione](/cli/azure/account#az-account-get-access-token).

Individuare la nuova app per le funzioni nel [portale di Azure] digitando il nome dell'app per le funzioni nella casella **Cerca** nella parte superiore della pagina e selezionando la risorsa **Servizio app**.

Selezionare la funzione **MyHttpTrigger**, selezionare **</> Recupera URL della funzione** > **predefinito (chiave di funzione)**  > **Copia**.

![Creare l'URL della funzione dal portale di Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

In questo URL la chiave di funzione è il parametro di query `code`. 

> [!NOTE]  
> Poiché l'app per le funzioni viene distribuita come contenitore, non è possibile apportare modifiche al codice della funzione nel portale. È invece necessario aggiornare il progetto nel contenitore locale e ripubblicarlo in Azure.

Incollare l'URL della funzione nella barra degli indirizzi del browser. Aggiungere il valore della stringa di query `&name=<yourname>` alla fine dell'URL e premere il tasto `Enter` per eseguire la richiesta. Nel browser dovrebbe venire visualizzata la risposta restituita dalla funzione.

L'esempio seguente mostra la risposta nel browser:

![Risposta della funzione nel browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

L'URL della richiesta include una chiave necessaria per impostazione predefinita per accedere a una funzione tramite HTTP. 

## <a name="enable-continuous-deployment"></a>adsa

Uno dei vantaggi dell'utilizzo dei contenitori è il supporto per la distribuzione continua. Funzioni di Azure consente di distribuire automaticamente gli aggiornamenti quando il contenitore viene aggiornato nel registro. Abilitare la distribuzione continua con il comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config).

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Questo comando restituisce l'URL del webhook di distribuzione dopo l'abilitazione della distribuzione continua. È anche possibile usare il comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) per ottenere questo URL. 

Copiare l'URL di distribuzione e passare al repository DockerHub, scegliere la scheda **Webhook**, digitare il **Nome webhook**, incollare l'URL in **URL webhook**e quindi scegliere il segno più ( **+** ).

![Aggiungere il webhook nel repository DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Con il webhook configurato, ogni volta che l'immagine collegata in DockerHub viene aggiornata l'app per le funzioni scarica e installa l'immagine.

## <a name="enable-ssh-connections"></a>Abilitare le connessioni SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Con SSH abilitato è possibile connettersi al contenitore tramite Strumenti avanzati (Kudu) del Servizio app. Per semplificare la connessione al contenitore tramite SSH, Funzioni di Azure fornisce un'immagine di base con SSH già abilitato. 

### <a name="change-the-base-image"></a>Modificare l'immagine di base

In Dockerfile aggiungere la stringa `-appservice` all'immagine di base nell'istruzione `FROM`, che per un progetto JavaScript avrà l'aspetto seguente.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Le differenze nelle due immagini di base abilitano le connessioni SSH nel contenitore. Queste differenze sono descritte in dettaglio in [questa esercitazione sul Servizio app](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Ricompilare e ridistribuire l'immagine

Nella cartella radice eseguire di nuovo il comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e come in precedenza sostituire `<docker-id>` con l'ID dell'account Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Eseguire il push dell'immagine aggiornata in Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

L'immagine aggiornata viene ridistribuita nell'app per le funzioni.

### <a name="connect-to-your-container-in-azure"></a>Connettersi al contenitore in Azure

Nel browser passare all'endpoint `scm.` di Strumenti avanzati (Kudu) seguente per il contenitore dell'app per le funzioni, sostituendo `<app_name>` con il nome dell'app per le funzioni.

```
https://<app_name>.scm.azurewebsites.net/
```

Accedere all'account Azure e quindi selezionare la scheda **SSH** per creare una connessione SSH nel contenitore.

Dopo aver stabilito la connessione, eseguire il comando `top` per visualizzare i processi attualmente in esecuzione. 

![Comando top di Linux in esecuzione in una sessione SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Eseguire la scrittura in archiviazione code

Funzioni di Azure consente di connettere i servizi di Azure e altre risorse alle funzioni senza la necessità di scrivere codice di integrazione personalizzato. Questi *binding*, che rappresentano sia input che output, vengono dichiarati all'interno della definizione di funzione. I dati dei binding vengono forniti alla funzione come parametri. Un *trigger* è un tipo speciale di binding di input. Anche se una funzione include un solo trigger, può avere più binding di input e output. Per altre informazioni, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

Questa sezione illustra come integrare la funzione con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

### <a name="download-the-function-app-settings"></a>Scaricare le impostazioni dell'app per le funzioni

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

Dato che si usa un binding di output di Archiviazione code, è necessario che l'estensione dei binding di archiviazione sia installata prima di eseguire il progetto. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Ad eccezione dei trigger HTTP e timer, i binding vengono implementati come pacchetti di estensione. Eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) seguente nella finestra del terminale per aggiungere il pacchetto di estensione di archiviazione nel progetto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Quando si usa Visual Studio, è anche possibile usare Gestione pacchetti NuGet per aggiungere il pacchetto.

---

A questo punto, è possibile aggiungere l'associazione di output di archiviazione al progetto.

### <a name="add-an-output-binding"></a>Aggiungere un binding di output

In Funzioni ogni tipo di binding richiede di definire `direction`, `type` e un valore univoco `name` nel file function.json. Il modo in cui si definiscono questi attributi dipende dal linguaggio dell'app per le funzioni.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

Una volta definito il binding, è possibile usare il relativo valore `name` per accedervi come attributo nella firma della funzione. Usando un binding di output, non è necessario usare il codice di Azure Storage SDK per l'autenticazione, per recuperare un riferimento alla coda o per scrivere dati. Queste attività vengono eseguite automaticamente dal runtime di Funzioni e dal binding di output.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Aggiornare il contenitore ospitato

Nella cartella radice eseguire di nuovo il comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e questa volta aggiornare la versione nel tag impostandola su `v1.0.2`. Come in precedenza, sostituire `<docker-id>` con l'ID dell'account Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Eseguire il push dell'immagine aggiornata nel repository.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Verificare gli aggiornamenti in Azure

Usare lo stesso URL usato in precedenza dal browser per attivare la funzione. Verrà visualizzata la stessa risposta. Questa volta, tuttavia, la stringa passata come parametro `name` viene scritta nella coda di archiviazione `outqueue`.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che il contenitore personalizzato è stato distribuito in un'app per le funzioni in Azure, è consigliabile leggere altre informazioni sugli argomenti seguenti:

+ [Monitoraggio delle funzioni](functions-monitoring.md)
+ [Opzioni di scalabilità e hosting](functions-scale.md)
+ [Hosting serverless basato su Kubernetes](functions-kubernetes-keda.md)

[Portale di Azure]: https://portal.azure.com
