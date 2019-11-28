---
title: Creare una funzione Python attivata da HTTP in Azure
description: Informazioni su come creare in Azure la prima funzione Python usando Azure Functions Core Tools e l'interfaccia della riga di comando di Azure.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 18ae1ed000ffe61ce1ea9ff5c18aae98a0ffae65
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227198"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Guida introduttiva: Creare una funzione Python attivata da HTTP in Azure

Questo articolo illustra come usare gli strumenti da riga di comando per creare un progetto Python che viene eseguito in Funzioni di Azure. Viene creata anche una funzione attivata da una richiesta HTTP. Dopo l'esecuzione in locale, il progetto viene infine pubblicato per essere eseguito come [funzione serverless](functions-scale.md#consumption-plan) in Azure. 

Questo articolo è il primo di due guide di avvio rapido di Python per Funzioni di Azure. Dopo aver completato questa guida di avvio rapido, è possibile [aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md) alla funzione.

È inoltre disponibile una [versione basata su Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01) di questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

+ Installare [Python 3.7.4](https://www.python.org/downloads/). Questa versione di Python è verificata con Funzioni. Python 3.8 e versioni precedenti non sono ancora supportati.

+ Installare [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 o versione successiva.

+ Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) 2.0.76 o versione successiva.

+ Avere una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Creare e attivare un ambiente virtuale

È consigliabile usare un ambiente Python 3.7 per sviluppare localmente le funzioni di Python. Eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`.

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

Un progetto per le funzioni può contenere più funzioni che condividono tutte le stesse configurazioni locali e di hosting.

Nell'ambiente virtuale eseguire i comandi seguenti:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

Il comando `func init` crea una cartella _MyFunctionProj_. Il progetto Python in questa cartella non dispone ancora di funzioni. Verranno aggiunte successivamente.

## <a name="create-a-function"></a>Creare una funzione

Per aggiungere una funzione al progetto, eseguire questo comando:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Questo comando crea una sottocartella denominata _HttpTrigger_ contenente i file seguenti:

* *function.json*: file di configurazione che definisce la funzione, il trigger e altre associazioni. In questo file il valore di `scriptFile` punta al file contenente la funzione, mentre la matrice `bindings` definisce le associazioni e il trigger di chiamata.

    Ogni associazione richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*: file di script che corrisponde alla funzione attivata tramite HTTP. Questo script ha un parametro `main()` predefinito. I dati HTTP del trigger vengono passati alla funzione mediante il `binding parameter` denominato `req`. Definito in function.json, l'elemento `req` è un'istanza della [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    L'oggetto restituito, definito come `$return` in *function.json*, è un'istanza della [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](functions-bindings-http-webhook.md).

A questo punto è possibile eseguire la nuova funzione nel computer locale.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Questo comando avvia l'app per le funzioni usando il runtime di Funzioni di Azure (func. exe):

```console
func host start
```

Le informazioni seguenti risulteranno scritte nell'output:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Copiare l'URL della funzione `HttpTrigger` dall'output e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta. Lo screenshot seguente mostra la risposta alla richiesta GET restituita dalla funzione locale al browser:

![Eseguire la verifica in locale nel browser](./media/functions-create-first-function-python/function-test-local-browser.png)

Premere CTRL+C per arrestare l'esecuzione dell'app per le funzioni.

Ora che la funzione è stata eseguita localmente, è possibile distribuirne il codice in Azure.  
Prima di poter distribuire l'app, è necessario creare alcune risorse di Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Creare un'app per le funzioni in Azure

Un'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. 

Eseguire il comando indicato di seguito. Sostituire `<APP_NAME>` con un nome univoco per l'app per le funzioni. Sostituire `<STORAGE_NAME>` con un nome per l'account di archiviazione. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. Questo nome deve essere univoco in tutte le app di Azure.

> [!NOTE]
> Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `myResourceGroup` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Il comando precedente crea un'app per le funzioni che esegue Python 3.7.4. Effettua inoltre il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse. È possibile usare questa istanza per monitorare l'app per le funzioni e visualizzare i log. 

A questo punto si è pronti per pubblicare il progetto di funzioni locale nell'app per le funzioni in Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Dopo aver creato l'app per le funzioni in Azure, è possibile usare il comando [func azure functionapp publish](functions-run-local.md#project-file-deployment) di Azure Functions Core Tools per implementare il codice del progetto in Azure. In questo esempio sostituire `<APP_NAME>` con il nome dell'app.

```console
func azure functionapp publish <APP_NAME> --build remote
```

L'opzione `--build remote` crea il progetto Python in remoto in Azure dai file del pacchetto di distribuzione (opzione consigliata). 

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
