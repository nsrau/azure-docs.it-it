---
title: Creare funzioni di Azure in Linux tramite un'immagine personalizzata
description: Informazioni su come creare Funzioni di Azure in esecuzione in un'immagine personalizzata di Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223728"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Creare una funzione in Linux tramite un contenitore personalizzato

In questa esercitazione si crea e si distribuisce codice Python in Funzioni di Azure come contenitore Docker personalizzato usando un'immagine di base di Linux. In genere si usa un'immagine personalizzata quando le funzioni richiedono una specifica versione del linguaggio oppure hanno una specifica dipendenza o configurazione non disponibile dall'immagine predefinita.

È anche possibile usare un contenitore predefinito del servizio app di Azure, come descritto in [Creare la prima funzione ospitata in Linux](functions-create-first-azure-function-azure-cli-linux.md). Le immagini di base supportate per Funzioni di Azure sono disponibili nel [repository di immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'app per le funzioni e un Dockerfile usando Azure Functions Core Tools.
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori.
> * Creare le risorse di supporto in Azure per l'app per le funzioni
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.
> * Abilitare la distribuzione continua.
> * Abilitare le connessioni SSH al contenitore.
> * Aggiungere un'associazione di output dell'archiviazione code. 

È possibile seguire questa esercitazione in qualsiasi computer che esegue Windows, Mac OS o Linux. Il completamento dell'esercitazione comporta il costo di alcuni dollari USA nell'account Azure.

## <a name="prerequisites"></a>Prerequisites

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.7.1846 o successiva
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.0.77 o successiva
- Il [runtime di Funzioni di Azure 2.x](functions-versions.md)
- I componenti di runtime dei linguaggi seguenti:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x o versione successiva](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 a 64 bit](https://www.python.org/downloads/release/python-3610/) o [Python 3.7 a 64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [ID Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

1. In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 2.7.1846 o successiva.
1. Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.0.76 o successiva.
1. Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.
1. Eseguire `docker login` per accedere a Docker. Questo comando non riesce se Docker non è in esecuzione, nel qual caso avviare Docker e riprovare.

## <a name="create-and-test-the-local-functions-project"></a>Creare e testare il progetto locale di funzioni

1. In un terminale o un prompt dei comandi creare una cartella per questa esercitazione in una posizione appropriata, quindi passare a tale cartella.

1. Seguire le istruzioni riportate in [Creare e attivare un ambiente virtuale](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) per creare un ambiente virtuale da usare per questa esercitazione.

1. Eseguire il comando seguente per il linguaggio scelto per creare un progetto di app per le funzioni in una cartella denominata `LocalFunctionsProject`. L'opzione `--docker` genera un `Dockerfile` per il progetto, che definisce un contenitore personalizzato adatto da usare con Funzioni di Azure e con il runtime selezionato.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Passare alla cartella del progetto:

    ```
    cd LocalFunctionsProject
    ```
    
1. Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione e l'argomento `--template` specifica il trigger della funzione. `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di codice appropriato per il linguaggio scelto del progetto e un file di configurazione denominato *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Per testare la funzione in locale, avviare l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionsProject*:
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Quando nell'output viene visualizzato l'endpoint `HttpExample`, passare a `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser dovrebbe essere visualizzato un messaggio simile a "Hello, Functions" (varia leggermente a seconda del linguaggio di programmazione scelto).

1. Premere **CTRL**-**C** per arrestare l'host.

## <a name="build-the-container-image-and-test-locally"></a>Creare l'immagine del contenitore e testarla localmente

1. (Facoltativo) Esaminare il *Dockerfile* nella cartella *LocalFunctionsProj*. Il Dockerfile descrive l'ambiente necessario per eseguire l'app per le funzioni in Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > L'elenco completo di immagini di base supportate per Funzioni di Azure è disponibile nella [pagina delle immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Nella cartella *LocalFunctionsProject* eseguire il comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e specificare il nome `azurefunctionsimage` e il tag `v1.0.0`. Sostituire `<docker_id>` con l'ID dell'account Docker Hub. Questo comando compila l'immagine Docker per il contenitore.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Al termine del comando, è possibile eseguire il nuovo contenitore in locale.
    
1. Per testare la compilazione, eseguire l'immagine in un contenitore locale usando il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/), sostituendo anche in questo caso `<docker_id>` con l'ID Docker e aggiungendo l'argomento `-p 8080:80` per le porte:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Quando l'immagine è in esecuzione in un contenitore locale, aprire un browser all'indirizzo `http://localhost:8080`, che dovrebbe visualizzare l'immagine segnaposto indicata di seguito. L'immagine viene visualizzata a questo punto perché la funzione è in esecuzione nel contenitore locale, allo stesso modo che in Azure, il che significa che è protetta da una chiave di accesso, definita nel file *function.json* con la proprietà `"authLevel": "function"`. Tuttavia, il contenitore non è stato ancora pubblicato in un'app per le funzioni in Azure, quindi la chiave non è ancora disponibile. Se si vuole eseguire il test in locale, arrestare Docker, impostare la proprietà di autorizzazione su `"authLevel": "anonymous"`, ricompilare l'immagine e riavviare Docker. Quindi reimpostare `"authLevel": "function"` in *function.json*. Per altre informazioni, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Immagine segnaposto che indica che il contenitore è in esecuzione in locale](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Dopo aver verificato l'app per le funzioni nel contenitore, arrestare Docker con **CTRL**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Eseguire il push dell'immagine a Docker Hub

Docker Hub è un registro contenitori che ospita immagini e fornisce servizi per immagini e contenitori. Per condividere l'immagine, operazione che include la distribuzione in Azure, è necessario eseguirne il push in un registro.

1. Se non è stato ancora fatto, eseguire l'accesso a Docker con il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), sostituendo `<docker_id>` con l'ID Docker. Questo comando richiede di immettere il nome utente e la password. Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito.

    ```
    docker login
    ```
    
1. Dopo avere effettuato l'accesso, eseguire il push dell'immagine in Docker Hub con il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/), anche in questo caso sostituendo `<docker_id>` con l'ID Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. A seconda delle velocità della rete, la prima volta che viene eseguito il push dell'immagine possono essere necessari alcuni minuti (il push delle modifiche successive sarà invece molto più rapido). Durante l'attesa, è possibile procedere con la sezione successiva e creare le risorse di Azure in un altro terminale.

## <a name="create-supporting-azure-resources-for-your-function"></a>Creare le risorse di Azure di supporto per la funzione

Per distribuire il codice della funzione in Azure, è necessario creare tre risorse:

- Un gruppo di risorse, ovvero un contenitore logico di risorse correlate.
- Un account di archiviazione di Azure, che mantiene lo stato e altre informazioni sul progetto.
- Un'app per le funzioni di Azure, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni si collega al progetto di funzione locale e consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione di risorse.

Per creare queste tre risorse, usare i comandi dell'interfaccia della riga di comando di Azure. Ogni comando fornisce output JSON al completamento.

1. Accedere ad Azure con il comando [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato `AzureFunctionsContainers-rg` nell'area `westeurope`. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile ricavata dal comando `az account list-locations`.

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `AzureFunctionsContainers-rg` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.
    
1. Creare un account di archiviazione per uso generico nel gruppo di risorse e nell'area con il comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). Nell'esempio seguente sostituire `<storage_name>` con un nome di app univoco a livello globale appropriato. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un tipico account per uso generico.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Per questa esercitazione, il costo dell'account di archiviazione è solo di pochi centesimi di dollaro USA.
    
1. Usare il comando per creare un piano Premium per Funzioni di Azure denominato `myPremiumPlan` nel piano tariffario **Elastico Premium 1** (`--sku EP1`), nell'area Europa occidentale (`-location westeurope` o un'area vicina alla propria) e in un contenitore Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    L'hosting Linux per i contenitori di funzioni personalizzate è supportato nei [piani dedicati (Servizio app)](functions-scale.md#app-service-plan) e nei [piani Premium](functions-premium-plan.md#features). In questo caso viene usato il piano Premium, che è scalabile secondo necessità. Per altre informazioni sull'hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md). Per calcolare i costi, vedere la [pagina dei prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/).

    Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Creare e configurare un'app per le funzioni in Azure con l'immagine

Un'app per le funzioni in Azure gestisce l'esecuzione delle funzioni nel piano di hosting. In questa sezione usare le risorse di Azure della sezione precedente per creare un'app per le funzioni da un'immagine in Docker Hub e configurarla con una stringa di connessione ad Archiviazione di Azure.

1. Creare l'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Nell'esempio seguente sostituire `<storage_name>` con il nome usato nella sezione precedente per l'account di archiviazione. Sostituire inoltre `<app_name>` con un nome di app univoco a livello globale appropriato e `<docker_id>` con l'ID Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Il parametro *deployment-container-image-name* specifica l'immagine da usare per l'app per le funzioni. È possibile usare il comando [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) per visualizzare informazioni sull'immagine usata per la distribuzione. È anche possibile usare il comando [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) per eseguire la distribuzione da un'immagine diversa.

1. Recuperare la stringa di connessione per l'account di archiviazione creato usando il comando [az storage account show-connection-string](/cli/azure/storage/account), assegnandola alla variabile della shell `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Aggiungere questa impostazione all'app per le funzioni usando il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Nel comando seguente sostituire `<app_name>` con il nome dell'app per le funzioni e sostituire `<connection_string>` con la stringa di connessione del passaggio precedente (una stringa codificata lunga che inizia con "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. La funzione può ora usare questa stringa di connessione per accedere all'account di archiviazione.

> [!TIP]
> In bash è possibile usare una variabile della shell per acquisire la stringa di connessione invece di usare gli Appunti. Prima di tutto, usare il comando seguente per creare una variabile con la stringa di connessione:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Fare quindi riferimento alla variabile nel secondo comando:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Se si pubblica l'immagine personalizzata in un account contenitore privato, è invece necessario usare le variabili di ambiente nel Dockerfile per la stringa di connessione. Per altre informazioni, vedere l'[istruzione ENV](https://docs.docker.com/engine/reference/builder/#env). È inoltre necessario impostare le variabili `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD`. Per usare i valori, è quindi necessario ricompilare l'immagine, effettuarne il push nel registro e quindi riavviare l'app per le funzioni in Azure.

## <a name="verify-your-functions-on-azure"></a>Verificare le funzioni in Azure

Con l'immagine distribuita nell'app per le funzioni in Azure, è ora possibile richiamare la funzione tramite richieste HTTP. Poiché la definizione di *function.json* include la proprietà `"authLevel": "function"`, è necessario prima di tutto ottenere la chiave di accesso (detta anche "chiave della funzione") e includerla come parametro URL in tutte le richieste all'endpoint.

1. Recuperare l'URL della funzione con la chiave di accesso (funzione) usando il portale di Azure o l'interfaccia della riga di comando di Azure con il comando `az rest`.

    # <a name="portal"></a>[Portale](#tab/portal)

    1. Accedere al portale di Azure, quindi individuare l'app per le funzioni immettendo il relativo nome nella casella **Cerca** nella parte superiore della pagina. Nei risultati selezionare la risorsa **Servizio app**.

    1. Nel riquadro di spostamento sinistro, in **Funzioni (sola lettura)** , selezionare il nome della funzione.

    1. Nel pannello dei dettagli selezionare **</> Recupera URL della funzione**:
    
        ![Comando Recupera URL della funzione nel portale di Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Nel popup selezionare **predefinita (Chiave di funzione)** e quindi **Copia**. La chiave è la stringa di caratteri che segue `?code=`.

        ![Copia dell'URL della funzione dal portale di Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Poiché l'app per le funzioni viene distribuita come contenitore, non è possibile apportare modifiche al codice della funzione nel portale. È invece necessario aggiornare il progetto nell'immagine locale, eseguire di nuovo il push dell'immagine nel registro e quindi ripetere la distribuzione in Azure. È possibile configurare la distribuzione continua in una sezione successiva.
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

    1. Creare una stringa di URL nel formato seguente, sostituendo `<subscription_id>`, `<resource_group>` e `<app_name>` rispettivamente con l'ID sottoscrizione di Azure, il gruppo di risorse dell'app per le funzioni e il nome dell'app per le funzioni:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Ad esempio, l'URL potrebbe essere simile all'indirizzo seguente:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Per praticità, è invece possibile assegnare l'URL a una variabile di ambiente e usarla nel comando `az rest`.
    
    1. Eseguire il comando `az rest` seguente (disponibile nell'interfaccia della riga di comando di Azure versione 2.0.77 e successive), sostituendo `<uri>` con la stringa di URI dell'ultimo passaggio, incluse le virgolette:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. L'output del comando è la chiave di funzione. L'URL completo della funzione è quindi `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, sostituendo `<app_name>`, `<function_name>` e `<key>` con i valori specifici.
    
        > [!NOTE]
        > La chiave recuperata qui è la chiave *host*, che è valida per tutte le funzioni nell'app per le funzioni. Il metodo illustrato per il portale recupera la chiave solo per un'unica funzione.

    ---

1. Incollare l'URL della funzione nella barra degli indirizzi del browser, aggiungendo il parametro `&name=Azure` alla fine. Nel browser verrà visualizzato un testo simile a "Hello Azure".

    ![Risposta della funzione nel browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Per testare l'autorizzazione, rimuovere il parametro code= dall'URL e verificare che non si ottengono risposte dalla funzione.


## <a name="enable-continuous-deployment-to-azure"></a>Abilitare la distribuzione continua in Azure

È possibile abilitare Funzioni di Azure per aggiornare automaticamente la distribuzione di un'immagine ogni volta che quest'ultima viene aggiornata nel registro.

1. Abilitare la distribuzione continua usando il comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config), sostituendo `<app_name>` con il nome dell'app per le funzioni:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Questo comando abilita la distribuzione continua e restituisce l'URL del webhook della distribuzione. È possibile recuperare questo URL in qualsiasi momento usando il comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url).

1. Copiare l'URL del webhook della distribuzione negli Appunti.

1. Aprire [Docker Hub](https://hub.docker.com/), accedere e selezionare **Repository** sulla barra di spostamento. Individuare e selezionare l'immagine, selezionare la scheda **Webhook**, specificare un valore per **Nome webhook**, incollare l'URL in **URL webhook** e quindi selezionare **Crea**:

    ![Aggiungere il webhook nel repository DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Con il webhook impostato, Funzioni di Azure ridistribuisce l'immagine ogni volta che viene aggiornata in Docker Hub.

## <a name="enable-ssh-connections"></a>Abilitare le connessioni SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Con SSH abilitato è possibile connettersi al contenitore tramite Strumenti avanzati (Kudu) del Servizio app. Per semplificare la connessione al contenitore tramite SSH, Funzioni di Azure fornisce un'immagine di base con SSH già abilitato. È necessario modificare solo il Dockerfile, quindi ricompilare e ridistribuire l'immagine. È quindi possibile connettersi al contenitore tramite Strumenti avanzati (Kudu)

1. Nel Dockerfile aggiungere la stringa `-appservice` all'immagine di base nell'istruzione `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Le differenze tra le immagini di base sono descritte nell'esercitazione [Servizi app - Immagini Docker personalizzate](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Ricompilare l'immagine usando di nuovo il comando `docker build`, sostituendo `<docker_id>` con l'ID Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Eseguire il push dell'immagine aggiornata in Docker Hub, che dovrebbe richiedere un tempo notevolmente inferiore rispetto al primo push, perché è necessario caricare solo i segmenti aggiornati dell'immagine.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Funzioni di Azure ridistribuisce automaticamente l'immagine nell'app per le funzioni. Il processo viene completato in meno di un minuto.

1. In un browser aprire `https://<app_name>.scm.azurewebsites.net/`, sostituendo `<app_name>` con il nome univoco. Questo URL è l'endpoint di Strumenti avanzati (Kudu) per il contenitore dell'app per le funzioni.

1. Accedere all'account Azure e quindi selezionare **SSH** per stabilire una connessione con il contenitore. La connessione può richiedere alcuni istanti se Azure sta ancora aggiornando l'immagine del contenitore.

1. Dopo aver stabilito la connessione con il contenitore, eseguire il comando `top` per visualizzare i processi attualmente in esecuzione. 

    ![Comando top di Linux in esecuzione in una sessione SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Scrivere in una coda dell'account di archiviazione di Azure

La soluzione Funzioni di Azure consente di connettere le funzioni ad altri servizi e risorse di Azure senza la necessità di scrivere codice di integrazione personalizzato. Questi *binding*, che rappresentano sia input che output, vengono dichiarati all'interno della definizione di funzione. I dati dei binding vengono forniti alla funzione come parametri. Un *trigger* è un tipo speciale di binding di input. Anche se una funzione include un solo trigger, può avere più binding di input e output. Per altre informazioni, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

Questa sezione illustra come integrare la funzione con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recuperare la stringa di connessione di Archiviazione di Azure

In precedenza è stato creato un account di archiviazione di Azure per l'uso da parte dell'app per le funzioni. La stringa di connessione per questo account è archiviata in modo sicuro nelle impostazioni dell'app in Azure. Scaricando l'impostazione nel file *local.settings.json*, è possibile usare tale connessione per scrivere in una coda di archiviazione nello stesso account quando si esegue la funzione localmente. 

1. Dalla radice del progetto eseguire il comando seguente, sostituendo `<app_name>` con il nome dell'app per le funzioni dall'argomento di avvio rapido precedente. Questo comando sovrascriverà tutti i valori esistenti nel file.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Aprire *local.settings.json* e copiare il valore denominato `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Il nome `AzureWebJobsStorage` e la stringa di connessione si usano in altre sezioni di questo articolo.

> [!IMPORTANT]
> Poiché *local.settings.json* contiene i segreti scaricati da Azure, escludere sempre questo file dal controllo del codice sorgente. Il file con estensione *gitignore* creato con un progetto di funzioni locale esclude il file per impostazione predefinita.

### <a name="add-an-output-binding-to-functionjson"></a>Aggiungere un binding di output a function.json

In Funzioni ogni tipo di binding richiede di definire `direction`, `type` e un valore univoco `name` nel file *function.json*. Il file *function.json* include già un binding di input per il tipo "httpTrigger" e un binding di output per la risposta HTTP. Per aggiungere un binding a una coda di archiviazione, modificare il file come indicato di seguito, aggiungendo un binding di output per il tipo "queue", per cui la coda viene visualizzata nel codice come argomento di input denominato `msg`. Il binding della coda richiede anche il nome della coda da usare, in questo caso `outqueue`, e il nome delle impostazioni che contengono la stringa di connessione, in questo caso `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

In un progetto di libreria di classi C# i binding vengono definiti come attributi di binding nel metodo della funzione. Il file *function.json* viene quindi generato automaticamente in base a questi attributi.

1. Per il binding della coda, eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) seguente per aggiungere il pacchetto di estensioni di archiviazione nel progetto.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Aprire il file *HttpTrigger.cs* e aggiungere l'istruzione `using` seguente:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Aggiungere il parametro seguente alla definizione del metodo `Run`:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    La definizione del metodo `Run` dovrà ora corrispondere al codice seguente:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Il parametro `msg` è un tipo `ICollector<T>`, che rappresenta una raccolta di messaggi scritti in un binding di output al completamento della funzione. In questo caso, l'output è una coda di archiviazione denominata `outqueue`. La stringa di connessione per l'account di archiviazione è impostata da `StorageAccountAttribute`. Questo attributo indica l'impostazione che contiene la stringa di connessione dell'account di archiviazione e può essere applicato a livello di classe, metodo o parametro. In questo caso, è possibile omettere `StorageAccountAttribute`, perché si usa già l'account di archiviazione predefinito.

::: zone-end

::: zone pivot="programming-language-javascript"

Aggiornare *function.json* come segue aggiungendo il binding della coda dopo il binding HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Aggiornare *function.json* come segue aggiungendo il binding della coda dopo il binding HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Aggiornare *function.json* come segue aggiungendo il binding della coda dopo il binding HTTP:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Aggiornare *function.json* come segue aggiungendo il binding della coda dopo il binding HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Aggiungere il codice per usare il binding di output

Dopo aver definito il binding, il relativo nome, in questo caso `msg`, viene visualizzato nel codice della funzione come argomento (o nell'oggetto `context` in JavaScript e TypeScript). È quindi possibile usare tale variabile per scrivere messaggi nella coda. È necessario scrivere codice per l'autenticazione, ottenendo un riferimento alla coda o scrivendo dati. Tutte queste attività di integrazione vengono gestite facilmente nel runtime di Funzioni di Azure e nel binding di output della coda.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Aggiornare l'immagine nel registro

1. Nella cartella radice eseguire di nuovo il comando `docker build` e questa volta aggiornare la versione nel tag impostandola su `v1.0.1`. Come prima, sostituire `<docker_id>` con l'ID dell'account Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Eseguire il push dell'immagine aggiornata nel repository con `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Poiché è stata configurata la distribuzione continua, aggiornando di nuovo l'immagine nel registro, viene automaticamente aggiornata anche l'app per le funzioni in Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visualizzare il messaggio nella coda di Archiviazione di Azure

In un browser usare lo stesso URL di prima per richiamare la funzione. Il browser deve visualizzare la stessa risposta precedente, perché questa parte del codice della funzione non è stata modificata. Il codice aggiunto, tuttavia, ha scritto un messaggio usando il parametro URL `name` nella coda di archiviazione `outqueue`.

È possibile visualizzare la coda nel [portale di Azure](../storage/queues/storage-quickstart-queues-portal.md) o in [Microsoft Azure Storage Explorer](https://storageexplorer.com/). È anche possibile visualizzare la coda nell'interfaccia della riga di comando di Azure come descritto nei passaggi seguenti:

1. Aprire il file *local.setting.json* del progetto di funzione e copiare il valore della stringa di connessione. In un terminale o una finestra di comando eseguire il comando seguente per creare una variabile di ambiente denominata `AZURE_STORAGE_CONNECTION_STRING`, incollando la stringa di connessione specifica al posto di `<connection_string>`. Questa variabile di ambiente implica che non è necessario fornire la stringa di connessione a ogni comando successivo usando l'argomento `--connection-string`.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Facoltativo) Usare il comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) per visualizzare le code di archiviazione dell'account. L'output di questo comando dovrebbe includere una coda denominata `outqueue`, che è stata creata quando la funzione ha scritto il primo messaggio in tale coda.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Usare il comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) per visualizzare i messaggi in questa coda, che dovrebbe essere il primo nome usato per il test della funzione in precedenza. Il comando recupera il primo messaggio nella coda in [codifica base64](functions-bindings-storage-queue-trigger.md#encoding), quindi è anche necessario decodificarlo per visualizzarlo come testo.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Poiché è necessario dereferenziare la raccolta di messaggi e decodificare da base64, eseguire PowerShell e usare il comando di PowerShell.

    ---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare a usare Funzioni di Azure usando le risorse create in questa esercitazione, lasciarle tutte invariate. Poiché è stato creato un piano Premium per Funzioni di Azure, verranno addebitati uno o due dollari USA al giorno in costi ricorrenti.

Per evitare i costi ricorrenti, eliminare il gruppo di risorse `AzureFunctionsContainer-rg` per pulire tutte le risorse nel gruppo: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Passaggi successivi

+ [Monitoraggio delle funzioni](functions-monitoring.md)
+ [Opzioni di scalabilità e hosting](functions-scale.md)
+ [Hosting serverless basato su Kubernetes](functions-kubernetes-keda.md)
