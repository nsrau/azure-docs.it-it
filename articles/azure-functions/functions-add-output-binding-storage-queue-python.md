---
title: Aggiungere un binding della coda di archiviazione di Azure alla funzione Python
description: Integrare una coda di Archiviazione di Azure con una funzione Python usando un binding di output.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 6cea44dca666bbf002de6e2b7dd283f49ac7bd5a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485166"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Aggiungere un binding della coda di archiviazione di Azure alla funzione Python

In questo articolo si integra una coda di Archiviazione di Azure con la funzione e l'account di archiviazione creati in [Creare una funzione Python attivata da HTTP in Azure](functions-create-first-function-python.md). È possibile ottenere questa integrazione usando un *binding di output* che scrive i dati di una richiesta HTTP in un messaggio della coda. Il completamento di questo articolo non comporta costi aggiuntivi oltre ai pochi centesimi di dollaro USA dell'argomento di avvio rapido precedente.

## <a name="prerequisites"></a>Prerequisites

- Completare l'argomento di avvio rapido [Creare una funzione Python attivata da HTTP in Azure](functions-create-first-function-python.md). Se è già stata eseguita la pulizia delle risorse alla fine di tale articolo, eseguire di nuovo i passaggi per ricreare l'app per le funzioni in Azure, ma lasciare invariate le risorse.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recuperare la stringa di connessione di Archiviazione di Azure

Quando è stata creata un'app per le funzioni in Azure nell'argomento di avvio rapido precedente, è stato creato anche un account di archiviazione. La stringa di connessione per questo account è archiviata in modo sicuro nelle impostazioni dell'app in Azure. Scaricando l'impostazione nel file *local.settings.json*, è possibile usare tale connessione per scrivere in una coda di archiviazione nello stesso account quando si esegue la funzione localmente. 

1. Dalla radice del progetto eseguire il comando seguente, sostituendo `<app_name>` con il nome dell'app per le funzioni dall'argomento di avvio rapido precedente. Questo comando sovrascriverà tutti i valori esistenti nel file.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Aprire *local.settings.json* e copiare il valore denominato `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Il nome `AzureWebJobsStorage` e la stringa di connessione si usano in altre sezioni di questo articolo.

> [!IMPORTANT]
> Poiché *local.settings.json* contiene i segreti scaricati da Azure, escludere sempre questo file dal controllo del codice sorgente. Il file con estensione *gitignore* creato con un progetto di funzioni locale esclude il file per impostazione predefinita.

## <a name="add-an-output-binding-to-functionjson"></a>Aggiungere un binding di output a function.json

Anche se una funzione può avere un solo trigger, può avere più binding di input e di output, che consentono di connettersi ad altri servizi e risorse di Azure senza scrivere codice di integrazione personalizzato. Questi binding vengono dichiarati nel file *function.json* nella cartella della funzione, a seconda del linguaggio usato per la funzione.

Dall'argomento di avvio rapido precedente il file *function.json* nella cartella *HttpExample* contiene due binding nella raccolta `bindings`:

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

Ogni binding ha almeno un tipo, una direzione e un nome. Nell'esempio precedente, il primo binding è di tipo `httpTrigger` con la direzione `in`. Per la direzione `in`, `name` specifica il nome di un parametro di input inviato alla funzione quando viene richiamato dal trigger.

Il secondo binding è di tipo `http` con la direzione `out`, nel qual caso il valore speciale `name` di `$return` indica che questo binding usa il valore restituito della funzione anziché fornire un parametro di input.

Per scrivere in una coda di Archiviazione di Azure da questa funzione, aggiungere un binding `out` di tipo `queue` con il nome `msg`, come illustrato nel codice seguente:

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

In questo caso, `msg` viene assegnato alla funzione come argomento di output. Per un tipo `queue`, è necessario specificare anche il nome della coda in `queueName` e fornire il *nome* della connessione di Archiviazione di Azure (da *local.settings.json*) in `connection`.

Per altre informazioni sui dettagli dei binding, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md) e la [configurazione dell'output della coda](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Aggiungere il codice per usare il binding di output

Con il binding della coda specificato nel file *function.json*, è ora possibile aggiornare la funzione per ricevere il parametro di output `msg` e scrivere i messaggi nella coda.

Aggiornare *HttpExample\\\_\_init\_\_.py* in modo che corrisponda al codice seguente, aggiungendo il parametro `msg` alla definizione della funzione e `msg.set(name)` sotto l'istruzione `if name:`.

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

Il parametro `msg` è un'istanza della [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Il relativo metodo `set` scrive un messaggio stringa nella coda, in questo caso il nome passato alla funzione nella stringa di query dell'URL.

Si noti che *non* è necessario scrivere codice per l'autenticazione, ottenendo un riferimento alla coda o scrivendo dati. Tutte queste attività di integrazione vengono gestite facilmente nel runtime di Funzioni di Azure e nel binding di output della coda.

## <a name="run-and-test-the-function-locally"></a>Eseguire e testare la funzione in locale

1. In un terminale o un prompt dei comandi passare alla cartella del progetto di funzione *LocalFunctionProj*.

1. Avviare l'host di runtime locale di Funzioni di Azure con il comando seguente.

    ```
    func host start
    ```

1. Al termine dell'avvio, viene visualizzato l'URL dell'endpoint `HttpExample`. Copiarne l'URL in un browser e aggiungere la stringa di query `?name=<your-name>`, rendendo l'URL completo come `http://localhost:7071/api/HttpExample?name=Guido`. Nel browser dovrebbe essere visualizzato un messaggio simile a `Hello Guido` come nell'articolo precedente.

    Se non viene visualizzato l'endpoint `HttpExample`, arrestare l'host con **CTRL**+**C** e verificare la presenza di errori nell'output. Ad esempio, l'host non attiverà l'endpoint se è presente un errore nel file *function.json*. Verificare inoltre che `func host start` venga eseguito dalla cartella del progetto di funzioni e non dalla cartella *HttpExample*.

1. Al termine, arrestare l'host con **CTRL**+**C**.

> [!TIP]
> Durante l'avvio, l'host scarica e installa l'[estensione di binding di archiviazione](functions-bindings-storage-blob.md#add-to-your-functions-app) e altre estensioni di binding Microsoft. Questa installazione si verifica perché le estensioni di binding vengono abilitate per impostazione predefinita nel file *host.json* con le proprietà seguenti:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Se si verificano errori relativi alle estensioni di binding, controllare che le proprietà sopra indicate siano presenti in *host.json*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visualizzare il messaggio nella coda di Archiviazione di Azure

Quando la funzione genera una risposta HTTP per il Web browser, chiama anche `msg.set(name)`, che scrive un messaggio in una coda di Archiviazione di Azure denominata `outqueue`, come specificato nel binding della coda. È possibile visualizzare la coda nel [portale di Azure](../storage/queues/storage-quickstart-queues-portal.md) o in [Microsoft Azure Storage Explorer](https://storageexplorer.com/). È anche possibile visualizzare la coda nell'interfaccia della riga di comando di Azure come descritto nei passaggi seguenti:

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
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
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
    
## <a name="redeploy-the-project-to-azure"></a>Ridistribuire il progetto in Azure

Ora che la funzione è stata testata in locale ed è stato verificato che ha scritto un messaggio nella coda di Archiviazione di Azure, è possibile ridistribuire il progetto per aggiornare l'endpoint in esecuzione in Azure.

1. Nella cartella *LocalFunctionsProj* usare il comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) per ridistribuire il progetto, sostituendo `<app_name>` con il nome dell'app.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Come nell'argomento di avvio rapido precedente, usare un browser o un CURL per testare la funzione ridistribuita.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Copiare l'**URL di richiamo** completo visualizzato nell'output del comando publish nella barra degli indirizzi di un browser, aggiungendo il parametro di query `&name=Azure`. Nel browser dovrebbe essere visualizzato un output simile a quello visualizzato quando è stata eseguita la funzione in locale.

    ![Output della funzione eseguita in Azure in un browser](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Eseguire [curl](https://curl.haxx.se/) con l'**URL di richiamo**, aggiungendo il parametro `&name=Azure`. L'output del comando dovrebbe essere il testo "Hello Azure".
    
    ![Output della funzione eseguita in Azure con curl](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Esaminare di nuovo la coda di archiviazione, come descritto nella sezione precedente, per verificare che contenga il nuovo messaggio scritto nella coda.

    Se si usa l'interfaccia della riga di comando di Azure per esaminare la coda, il comando `az storage message peek` mostra solo il primo messaggio nella coda. Per simulare l'elaborazione dei messaggi, usare invece `az storage message get` con tutti gli stessi argomenti. Il comando `get` restituisce il messaggio e lo rimuove dalla coda. È quindi possibile ripetere lo stesso comando fino a quando la coda non è vuota (e il comando restituisce un errore).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si continua con il passaggio successivo, [Abilitare l'integrazione di Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource), mantenere tutte le risorse esistenti per poterle riutilizzare.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Abilitare l'integrazione di Application Insights con un'app Funzioni di Azure](functions-monitoring.md#manually-connect-an-app-insights-resource)

Altre risorse:

- [Esempi di progetti di Funzioni completi in Python](/samples/browse/?products=azure-functions&languages=python)
- [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md)
- [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
- [Pagina dei prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/)
- Articolo [Stima dei costi del piano a consumo](functions-consumption-costs.md)
