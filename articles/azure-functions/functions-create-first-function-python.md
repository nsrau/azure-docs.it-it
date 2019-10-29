---
title: Creare una funzione attivata tramite HTTP in Azure
description: Informazioni su come creare in Azure la prima funzione Python usando Azure Functions Core Tools e l'interfaccia della riga di comando di Azure.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: f2602e5a13f83090291656e7062c74c245bc6568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693339"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Creare una funzione attivata tramite HTTP in Azure

Questo articolo illustra come usare gli strumenti da riga di comando per creare un progetto Python che viene eseguito in Funzioni di Azure. Viene creata anche una funzione attivata da una richiesta HTTP. Il progetto viene infine pubblicato per essere eseguito come [funzione serverless](functions-scale.md#consumption-plan) in Azure.

Questo articolo è il primo di due guide di avvio rapido di Python per Funzioni di Azure. Dopo aver completato questa guida di avvio rapido, è possibile [aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md) alla funzione.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

+ Installare [Python 3.6.8](https://www.python.org/downloads/). Questa versione di Python è verificata con Funzioni. La versione 3.7 e le versioni precedenti non sono ancora supportate.

+ Installare [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.7.1575 o successiva.

+ Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.x o successiva.

+ Avere una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Creare e attivare un ambiente virtuale (facoltativo)

È consigliabile usare un ambiente Python 3.6.x per sviluppare localmente le funzioni di Python. Eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`.

> [!NOTE]
> Se Python non ha installato venv nella distribuzione Linux, è possibile eseguirne l'installazione usando il comando seguente:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash:

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell o un prompt dei comandi di Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Ora che è stato attivato l'ambiente virtuale, eseguire i comandi rimanenti. Per uscire dall'ambiente virtuale, eseguire `deactivate`.

## <a name="create-a-local-functions-project"></a>Creare un progetto per le funzioni locale

Un progetto per le funzioni è l'equivalente di un'app per le funzioni in Azure. Può contenere più funzioni che condividono tutte le stesse configurazioni locali e di hosting.

1. Nell'ambiente virtuale eseguire il comando seguente:

    ```console
    func init MyFunctionProj
    ```

1. Selezionare **python** come runtime del ruolo di lavoro.

    Il comando crea una cartella _MyFunctionProj_. Contiene i tre file seguenti:

    * *local.settings.json*: viene usato per archiviare le impostazioni dell'app e le stringhe di connessione durante l'esecuzione in locale. Questo file non viene pubblicato in Azure.
    * *requirements.txt*: contiene l'elenco dei pacchetti che il sistema installerà durante la pubblicazione in Azure.
    * *host.json*: contiene le opzioni di configurazione globali che interessano tutte le funzioni in un'app per le funzioni. Questo file viene pubblicato in Azure.

1. Passare alla nuova cartella *MyFunctionProj*:

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Creare una funzione

Aggiungere una funzione al nuovo progetto.

1. Per aggiungere una funzione al progetto, eseguire questo comando:

    ```console
    func new
    ```

1. Usare la freccia in giù per selezionare il modello **Trigger HTTP**.

1. Quando viene chiesto di specificare un nome di funzione, immettere *HttpTrigger* e quindi premere INVIO.

Questi comandi creano una sottocartella denominata _HttpTrigger_. Contiene i file seguenti:

* *function.json*: file di configurazione che definisce la funzione, il trigger e altre associazioni. In questo file il valore di `scriptFile` punta al file contenente la funzione, mentre la matrice `bindings` definisce le associazioni e il trigger di chiamata.

    Ogni associazione richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*: file di script che corrisponde alla funzione attivata tramite HTTP. Questo script ha un parametro `main()` predefinito. I dati HTTP del trigger vengono passati alla funzione mediante il `binding parameter` denominato `req`. Definito in function.json, l'elemento `req` è un'istanza della [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    L'oggetto restituito, definito come `$return` in *function.json*, è un'istanza della [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

La funzione viene eseguita localmente tramite il runtime di Funzioni di Azure.

1. Questo comando avvia l'app per le funzioni:

    ```console
    func host start
    ```

    All'avvio, l'host di Funzioni di Azure visualizza un output simile al seguente, che è stato troncato per migliorarne la leggibilità:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Copiare l'URL della funzione `HttpTrigger` dall'output del runtime e incollarlo nella barra degli indirizzi del browser.

1. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta. Lo screenshot seguente mostra la risposta alla richiesta GET restituita dalla funzione locale al browser:

    ![Eseguire la verifica in locale nel browser](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Premere CTRL+C per arrestare l'app per le funzioni.

Ora che è stata eseguita la funzione in locale, è possibile creare l'app della funzione e altre risorse necessarie in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Creare un'app per le funzioni in Azure

Un'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse.

Eseguire il comando indicato di seguito. Sostituire `<APP_NAME>` con un nome univoco per l'app per le funzioni. Sostituire `<STORAGE_NAME>` con un nome per l'account di archiviazione. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. Questo nome deve essere univoco in tutte le app di Azure.

> [!NOTE]
> Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `myResourceGroup` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Il comando precedente effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse. È possibile usare questa istanza per monitorare l'app per le funzioni e visualizzare i log.

A questo punto si è pronti per pubblicare il progetto di funzioni locale nell'app per le funzioni in Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Dopo aver creato l'app per le funzioni in Azure, è possibile usare il comando [func azure functionapp publish](functions-run-local.md#project-file-deployment) di Azure Functions Core Tools per implementare il codice del progetto in Azure. In questo esempio sostituire `<APP_NAME>` con il nome dell'app.

```console
func azure functionapp publish <APP_NAME> --build remote
```

L'opzione `--build remote` crea il progetto Python in remoto in Azure dai file del pacchetto di distribuzione. 

L'output sarà simile al messaggio seguente, che è stato troncato per migliorarne la leggibilità:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

È possibile copiare il valore di `Invoke url` per `HttpTrigger` e usarlo per verificare la funzione in Azure. L'URL contiene un valore di stringa di query `code` che rappresenta la chiave della funzione, che rende difficile per gli altri utenti chiamare l'endpoint del trigger HTTP in Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Per visualizzare i log quasi in tempo reale per un'app Python pubblicata, usare la funzionalità [Live Metrics Stream di Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto di funzioni Python con una funzione attivata tramite HTTP. Il progetto è stato quindi eseguito in un computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md)
