---
title: Creare una funzione Python attivata da HTTP in Azure
description: Creare e distribuire codice Python serverless nel cloud con Funzioni di Azure.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: c665f807d78c699423db457bf57dca2f16109913
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898573"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Avvio rapido: Creare una funzione Python attivata da HTTP in Azure

In questo articolo vengono usati gli strumenti da riga di comando per creare una funzione Python che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure. Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione basata su Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python) di questo articolo.

## <a name="prerequisites"></a>Prerequisites

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.7.1846 o successiva.
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.0.76 o successiva. 
- [Python 3.7.4 - 64 bit](https://www.python.org/downloads/release/python-374/) (Python 3.7.4 è stato verificato con Funzioni di Azure; Python 3.8 e versioni successive non sono ancora supportati).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

1. In un terminale o una finestra di comando eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 2.7.1846 o successiva.
1. Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.0.76 o successiva.
1. Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.
1. Eseguire `python --version` (Linux/MacOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.7.x.

## <a name="create-and-activate-a-virtual-environment"></a>Creare e attivare un ambiente virtuale

In una cartella appropriata eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`. Assicurarsi di usare Python 3.7 che è supportato da Funzioni di Azure.


# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se Python non ha installato il pacchetto venv nella distribuzione Linux, eseguire il comando seguente:

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

È possibile eseguire tutti i comandi successivi in questo ambiente virtuale attivato. Per uscire dall'ambiente virtuale, eseguire `deactivate`.

## <a name="create-a-local-function-project"></a>Creare un progetto di funzione locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più funzioni singole che rispondono ognuna a un trigger specifico. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione.

1. Nell'ambiente virtuale eseguire il comando `func init` per creare un progetto di funzione in una cartella denominata *LocalFunctionProj* con il runtime specificato:

    ```
    func init LocalFunctionProj --python
    ```
    
    Questa cartella contiene vari file per il progetto, inclusi i file di configurazione denominati [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, il file viene escluso dal controllo del codice sorgente per impostazione predefinita nel file con estensione *gitignore*.

    > [!TIP]
    > Poiché un progetto di funzione è associato a un runtime specifico, tutte le funzioni del progetto devono essere scritte con lo stesso linguaggio.

1. Passare alla cartella del progetto:

    ```
    cd LocalFunctionProj
    ```
    
1. Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione e l'argomento `--template` specifica il trigger della funzione. `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di codice appropriato per il linguaggio scelto del progetto e un file di configurazione denominato *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(Facoltativo) Esaminare il contenuto del file

Se si vuole, è possibile passare a [Eseguire la funzione localmente](#run-the-function-locally) ed esaminare il contenuto del file in un secondo momento.

### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contiene una funzione Python `main()` attivata in base alla configurazione presente in *function.json*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Per il trigger HTTP, la funzione riceve i dati della richiesta nella variabile `req` come definito in *function.json*. `req` è un'istanza della [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). L'oggetto restituito, definito come `$return` in *function.json*, è un'istanza della [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](functions-bindings-http-webhook.md).

### <a name="functionjson"></a>function.json

*function.json* è un file di configurazione che definisce gli oggetti `bindings` di input e output per la funzione, incluso il tipo di trigger. Se si vuole, è possibile modificare `scriptFile` per richiamare un file Python diverso.

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
    }
  ]
}
```

Ogni binding richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Avviare la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionProj*:

```
func start
```

Verrà visualizzato l'output seguente. Se HttpExample non viene visualizzato come illustrato di seguito, è probabile che l'host sia stato avviato dall'interno della cartella *HttpExample*. In tal caso, usare **CTRL**+**C** per arrestare l'host, passare alla cartella padre *LocalFunctionProj* ed eseguire di nuovo `func start`.

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Copiare l'URL della funzione `HttpExample` da questo output in un browser e aggiungere la stringa di query `?name=<your-name>`, rendendo l'URL completo come `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser dovrebbe essere visualizzato un messaggio simile a `Hello Functions`:

![Risultato della funzione eseguita in locale nel browser](./media/functions-create-first-function-python/function-test-local-browser.png)

Il terminale in cui è stato eseguito `func start` mostra anche l'output del log quando si effettuano le richieste.

Quando si è pronti, usare **CTRL**+**C** per arrestare l'host di Funzioni.

## <a name="create-supporting-azure-resources-for-your-function"></a>Creare le risorse di Azure di supporto per la funzione

Per distribuire il codice della funzione in Azure, è necessario creare tre risorse:

- Un gruppo di risorse, ovvero un contenitore logico di risorse correlate.
- Un account di archiviazione di Azure, che mantiene lo stato e altre informazioni sul progetto.
- Un'app per le funzioni di Azure, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni si collega al progetto di funzione locale e consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione di risorse.

Per creare queste tre risorse, usare i comandi dell'interfaccia della riga di comando di Azure. Ogni comando fornisce output JSON al completamento.

1. Se non è già stato fatto, accedere ad Azure con il comando [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato `AzureFunctionsQuickstart-rg` nell'area `westeurope`. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile ricavata dal comando `az account list-locations`.

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `AzureFunctionsQuickstart-rg` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.
    
1. Creare un account di archiviazione per uso generico nel gruppo di risorse e nell'area con il comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). Nell'esempio seguente sostituire `<storage_name>` con un nome di app univoco a livello globale appropriato. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un tipico account per uso generico.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Per questo argomento di avvio rapido, il costo dell'account di archiviazione è solo di pochi centesimi di dollaro USA.
    
1. Creare l'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Nell'esempio seguente, sostituire `<storage_name>` con il nome dell'account usato nel passaggio precedente e sostituire `<app_name>` con un nome univoco a livello globale appropriato. `<app_name>` è anche il dominio DNS predefinito per l'app per le funzioni.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Questo comando crea un'app per le funzioni che esegue il runtime del linguaggio specificato nel [Piano a consumo di Funzioni di Azure](functions-scale.md#consumption-plan), che è gratuito per la quantità di utilizzo prevista in questo argomento. Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.
    
## <a name="deploy-the-function-project-to-azure"></a>Distribuire il progetto di funzione in Azure

Con le risorse necessarie, è ora possibile distribuire il progetto di funzione locale nell'app per le funzioni in Azure usando il comando [func azure functionapp publish](functions-run-local.md#project-file-deployment). Nell'esempio seguente sostituire `<app_name>` con il nome dell'app.

```
func azure functionapp publish <app_name>
```

Se viene visualizzato l'errore "Non è possibile trovare l'app con nome...", attendere alcuni secondi e riprovare, perché Azure potrebbe non avere inizializzato completamente l'app dopo il comando `az functionapp create` precedente.

Il comando publish mostra risultati simili a quelli riportati nell'output seguente (troncato per semplicità):

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Richiamare la funzione in Azure

Poiché la funzione usa un trigger HTTP, è possibile richiamarla eseguendo una richiesta HTTP al relativo URL nel browser o con uno strumento come curl. In entrambe i casi, il parametro URL `code` è la chiave di funzione univoca che autorizza la chiamata all'endpoint della funzione.

# <a name="browsertabbrowser"></a>[Browser](#tab/browser)

Copiare l'**URL di richiamo** completo visualizzato nell'output del comando publish nella barra degli indirizzi di un browser, aggiungendo il parametro di query `&name=Azure`. Nel browser dovrebbe essere visualizzato un output simile a quello visualizzato quando è stata eseguita la funzione in locale.

![Output della funzione eseguita in Azure in un browser](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

Eseguire [curl](https://curl.haxx.se/) con l'**URL di richiamo**, aggiungendo il parametro `&name=Azure`. L'output del comando dovrebbe essere il testo "Hello Azure".

![Output della funzione eseguita in Azure con curl](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Per visualizzare i log quasi in tempo reale per un'app Python pubblicata, usare la funzionalità [Live Metrics Stream di Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si continua con il passaggio successivo, [Aggiungere un binding di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md), mantenere tutte le risorse esistenti per poterle riutilizzare.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-python.md)
