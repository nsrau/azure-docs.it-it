---
title: Creare una funzione attivata tramite HTTP in Azure
description: Informazioni su come creare in Azure la prima funzione Python usando Azure Functions Core Tools e l'interfaccia della riga di comando di Azure.
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: cb7f5a10169c8baaecae0fc1916a439d61bfbf7c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170858"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Creare una funzione attivata tramite HTTP in Azure

Questo articolo illustra come usare gli strumenti da riga di comando per creare un progetto Python che viene eseguito in Funzioni di Azure. La funzione creata viene attivata da richieste HTTP. Il progetto viene infine pubblicato per essere eseguito come [funzione serverless](functions-scale.md#consumption-plan) in Azure.

Questo articolo è il primo di due argomenti di avvio rapido per Funzioni di Azure. Dopo aver completato questo articolo, si [aggiunge un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md) alla funzione.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario disporre degli elementi seguenti:

+ Installare [Python 3.6.x](https://www.python.org/downloads/).

+ Installare [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.7.1575 o successiva.

+ Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.x o successiva.

+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Creare e attivare un ambiente virtuale (facoltativo)

Per sviluppare e testare funzioni Python in locale, è consigliabile usare un ambiente Python 3.6. Eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`.

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell o un prompt dei comandi di Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

I comandi rimanenti vengono eseguiti all'interno dell'ambiente virtuale.

## <a name="create-a-local-functions-project"></a>Creare un progetto Funzioni locale

Un progetto Funzioni è l'equivalente di un'app per le funzioni in Azure. Può contenere più funzioni che condividono tutte le stesse configurazioni locali e di hosting.

Nell'ambiente virtuale eseguire questo comando scegliendo **python** come runtime del ruolo di lavoro.

```console
func init MyFunctionProj
```

Verrà creata una cartella denominata _MyFunctionProj_ contenente i tre file seguenti:

* `local.settings.json` viene usato per archiviare le impostazioni dell'app e le stringhe di connessione durante l'esecuzione in locale. Questo file non viene pubblicato in Azure.
* `requirements.txt` contiene l'elenco di pacchetti da installare alla pubblicazione in Azure.
* `host.json` contiene le opzioni di configurazione globali che interessano tutte le funzioni in un'app per le funzioni. Questo file viene pubblicato in Azure.

Passare alla nuova cartella MyFunctionProj:

```console
cd MyFunctionProj
```

## <a name="create-a-function"></a>Creare una funzione

Per aggiungere una funzione al progetto, eseguire questo comando:

```console
func new
```

Scegliere il modello **trigger HTTP**, digitare `HttpTrigger` come nome della funzione e quindi premere Invio.

Verrà creata una sottocartella denominata _HttpTrigger_ contenente i file seguenti:

* **function.json**: file di configurazione che definisce la funzione, il trigger e altre associazioni. Esaminare questo file e verificare che il valore di `scriptFile` punti al file contenente la funzione, mentre le associazioni e il trigger di chiamata sono definiti nella matrice `bindings`.

  Ogni associazione richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook.md#output).

* **\_\_init\_\_.py**: file di script che corrisponde alla funzione attivata tramite HTTP. Esaminare lo script e verificare che contenga un elemento `main()` predefinito. I dati HTTP del trigger vengono passati a questa funzione mediante il parametro denominato di associazione `req`. Definito in function.json, l'elemento `req` è un'istanza della [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    L'oggetto restituito, definito come `$return` in function.json, è un'istanza della [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Il comando seguente avvia l'app per le funzioni, che viene eseguita in locale usando lo stesso runtime di Funzioni di Azure presente in Azure.

```console
func host start
```

All'avvio, l'host di Funzioni visualizza un output simile al seguente, che è stato troncato per migliorarne la leggibilità:

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

Copiare l'URL della funzione `HttpTrigger` dall'output del runtime e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta. Di seguito è illustrata la risposta nel browser alla richiesta GET restituita dalla funzione locale:

![Testare in locale nel browser](./media/functions-create-first-function-python/function-test-local-browser.png)

Ora che è stata eseguita la funzione in locale, è possibile creare l'app della funzione e altre risorse necessarie in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Creare un'app per le funzioni in Azure

Un'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse.

Eseguire il comando seguente sostituendo il segnaposto `<APP_NAME>` con un nome univoco dell'app per le funzioni e il segnaposto `<STORAGE_NAME>` con il nome dell'account di archiviazione. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. Questo nome deve essere univoco in tutte le app di Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> Le app Linux e Windows non possono essere ospitate nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `myResourceGroup` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.

Questo comando effettuerà anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, che potrà essere usata per il monitoraggio e la visualizzazione dei log.

A questo punto si è pronti per pubblicare il progetto di funzioni locale nell'app per le funzioni in Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Dopo aver creato l'app per le funzioni in Azure, è possibile usare il comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) degli strumenti di base per implementare il codice del progetto in Azure. In questi esempi sostituire `<APP_NAME>` con il nome dell'app creata nel passaggio precedente.

```command
func azure functionapp publish <APP_NAME> --build remote
```

L'opzione `--build remote` crea il progetto Python in remoto in Azure dai file del pacchetto di distribuzione. 

Verrà visualizzato un output simile al seguente, che è stato troncato per migliorarne la leggibilità:

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

Copiare il valore di `Invoke url` per `HttpTrigger`, che è ora possibile usare per testare la funzione in Azure. L'URL contiene un valore della stringa di query `code` che rappresenta la chiave di funzione. Questa chiave rende più difficile ad altri utenti chiamare l'endpoint per il trigger HTTP in Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Per visualizzare i log quasi in tempo reale per un'app Python pubblicata, è consigliabile usare la funzionalità [Live Metrics Stream di Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto di funzioni Python con una funzione attivata tramite HTTP. Il progetto è stato quindi eseguito in un computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md)
