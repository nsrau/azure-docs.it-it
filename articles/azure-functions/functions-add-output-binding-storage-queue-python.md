---
title: Aggiungere un binding della coda di archiviazione di Azure alla funzione Python
description: Informazioni su come aggiungere un binding di output della coda di archiviazione di Azure alla funzione Python usando l'interfaccia della riga di comando di Azure e Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 9fdbf3466256c5e24de17541770fa2095fcf38a4
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171076"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Aggiungere un binding della coda di archiviazione di Azure alla funzione Python

La soluzione Funzioni di Azure consente di connettere i servizi di Azure e altre risorse alle funzioni senza la necessità di scrivere codice di integrazione personalizzato. Questi *binding*, che rappresentano sia input che output, vengono dichiarati all'interno della definizione di funzione. I dati dei binding vengono forniti alla funzione come parametri. Un *trigger* è un tipo speciale di binding di input. Anche se una funzione include un solo trigger, può avere più binding di input e output. Per altre informazioni, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

Questo articolo illustra come integrare la funzione creata nel [precedente articolo di avvio rapido](functions-create-first-function-python.md) con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

La maggior parte dei binding richiede una stringa di connessione archiviata che verrà usata da Funzioni per accedere al servizio associato. Per semplificare questa connessione, usare l'account di archiviazione creato con l'app per le funzioni. La connessione a questo account è già archiviata in un'impostazione dell'app denominata `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare con questo articolo, completare i passaggi della [parte 1 dell'articolo di avvio rapido di Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Scaricare le impostazioni dell'app per le funzioni

Nel precedente argomento di avvio rapido è stata creata un'app per le funzioni in Azure insieme all'account di archiviazione necessario. La stringa di connessione per questo account è archiviata in modo sicuro nelle impostazioni dell'app in Azure. In questo articolo verranno scritti messaggi in una coda di archiviazione dello stesso account. Per connettersi all'account di archiviazione durante l'esecuzione della funzione in locale, è necessario scaricare le impostazioni dell'app nel file local.settings.json. Eseguire il comando di Azure Functions Core Tools seguente per scaricare le impostazioni nel file local.settings.json, sostituendo `<APP_NAME>` con il nome dell'app per le funzioni specificato nell'articolo precedente:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Potrebbe essere necessario accedere all'account Azure.

> [!IMPORTANT]  
> Il file local.settings.json contiene segreti, quindi non viene mai pubblicato e dovrà essere escluso dal controllo del codice sorgente.

È necessario il valore `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Usare questa connessione per verificare se il binding di output funziona come previsto.

## <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

È ora possibile aggiungere il binding di output di archiviazione nel progetto.

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

In Funzioni ogni tipo di binding richiede di definire `direction`, `type` e un valore univoco `name` nel file function.json. A seconda del tipo di binding, potrebbero essere necessarie altre proprietà. La tabella di [configurazione dell'output della coda](functions-bindings-storage-queue.md#output---configuration) indica i campi necessari per un binding della coda di archiviazione di Azure.

Per creare un binding, aggiungere un oggetto configurazione di binding al file function.json. Modificare il file function.json nella cartella HttpTrigger per aggiungere un oggetto alla matrice `bindings` che include queste proprietà:

| Proprietà | Valore | DESCRIZIONE |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Il nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **`type`** | `queue` | Il binding è un binding della coda di archiviazione di Azure. |
| **`direction`** | `out` | Il binding è un binding di output. |
| **`queueName`** | `outqueue` | Il nome della coda in cui scrive il binding. Se `queueName` non esiste, il binding lo crea al primo utilizzo. |
| **`connection`** | `AzureWebJobsStorage` | Il nome dell'impostazione dell'app che contiene la stringa di connessione dell'account di archiviazione. L'impostazione `AzureWebJobsStorage` contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

Il file function.json dovrà avere un aspetto simile a questo esempio:

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

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

Una volta configurato, è possibile usare il valore `name` per accedere al binding come attributo di metodo nella firma della funzione. Nell'esempio seguente `msg` è un'istanza della [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest).

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

Quando si usa un binding di output, non è necessario usare il codice di Azure Storage SDK per l'autenticazione, per recuperare un riferimento alla coda o per scrivere dati. Queste attività vengono eseguite automaticamente dal runtime di Funzioni e dal binding di output.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Come prima, usare il comando seguente per avviare il runtime di Funzioni in locale:

```bash
func host start
```

> [!NOTE]  
> Nell'argomento di avvio rapido precedente è stato necessario abilitare i bundle di estensioni nel file host.json, quindi durante l'avvio è stata scaricata e configurata l'[estensione di binding di archiviazione](functions-bindings-storage-blob.md#packages---functions-2x), nonché altre estensioni di binding di Microsoft.

Copiare l'URL della funzione `HttpTrigger` dall'output del runtime e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` a questo URL ed eseguire la richiesta. Nel browser dovrebbe essere visualizzata la stessa risposta dell'articolo precedente.

Questa volta il binding di output crea anche una coda denominata `outqueue` nell'account di archiviazione e aggiunge un messaggio con questa stessa stringa.

Quindi, usare l'interfaccia della riga di comando di Azure per visualizzare la nuova coda e verificare che è stato aggiunto un messaggio. È anche possibile visualizzare la coda usando [Microsoft Azure Storage Explorer][Azure Storage Explorer] oppure nel [portale di Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Impostare la connessione dell'account di archiviazione

Aprire il file local.settings.json e copiare il valore di `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Impostare la variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING` sulla stringa di connessione usando il comando Bash seguente:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Con la stringa di connessione impostata sulla variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING`, è possibile accedere all'account di archiviazione senza eseguire ogni volta l'autenticazione.

### <a name="query-the-storage-queue"></a>Eseguire una query sulla coda di archiviazione

È possibile usare il comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) per visualizzare le code di archiviazione dell'account, come indicato nell'esempio seguente:

```azurecli-interactive
az storage queue list --output tsv
```

L'output di questo comando include una coda denominata `outqueue`, che è la coda creata quando è stata eseguita la funzione.

Usare quindi il comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) per visualizzare i messaggi inclusi in questa coda, come indicato in questo esempio:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

La stringa restituita dovrebbe essere la stessa del messaggio inviato per testare la funzione.

> [!NOTE]  
> L'esempio precedente decodifica la stringa restituita da base64. Il motivo è che i binding di archiviazione della coda scrivono e leggono i dati da Archiviazione di Azure sotto forma di [stringhe base64](functions-bindings-storage-queue.md#encoding).

Ora è il momento di ripubblicare l'app per le funzioni aggiornata in Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Anche in questo caso, è possibile usare cURL o un browser per testare la funzione distribuita. Come prima, aggiungere la stringa di query `&name=<yourname>` all'URL, come indicato in questo esempio:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

È possibile [esaminare il messaggio della coda di archiviazione](#query-the-storage-queue) per verificare che il binding di output genera anche in questo caso un nuovo messaggio nella coda.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

La funzione di trigger HTTP è stata aggiornata per scrivere dati in una coda di archiviazione. Per altre informazioni sullo sviluppo di Funzioni di Azure con Python, vedere [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md) e [Trigger e binding di Funzioni di Azure](functions-triggers-bindings.md). Per alcuni esempi di progetti di Funzioni completi in Python, vedere gli [esempi di funzioni Python](/samples/browse/?products=azure-functions&languages=python). 

Quindi, è consigliabile abilitare il monitoraggio di Application Insights per l'app per le funzioni:

> [!div class="nextstepaction"]
> [Abilitare l'integrazione di Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/