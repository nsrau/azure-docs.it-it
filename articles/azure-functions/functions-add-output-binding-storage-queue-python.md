---
title: Aggiungere un binding della coda di archiviazione di Azure alla funzione Python
description: Informazioni su come aggiungere un binding della coda di archiviazione di Azure alla funzione Python.
ms.date: 10/02/2019
ms.topic: quickstart
ms.openlocfilehash: dede135da56e9ed1eaaed2ae0f7b5cd14d08195c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231243"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Aggiungere un binding della coda di archiviazione di Azure alla funzione Python

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Questo articolo illustra come integrare la funzione creata nel [precedente articolo di avvio rapido](functions-create-first-function-python.md) con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

La maggior parte dei binding richiede una stringa di connessione archiviata che verrà usata da Funzioni per accedere al servizio associato. Per semplificare questa connessione, usare l'account di archiviazione creato con l'app per le funzioni. La connessione a questo account è già archiviata in un'impostazione dell'app denominata `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare con questo articolo, completare i passaggi della [parte 1 dell'articolo di avvio rapido di Python](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Scaricare le impostazioni dell'app per le funzioni

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

È ora possibile aggiungere il binding di output di archiviazione nel progetto.

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

In Funzioni ogni tipo di binding richiede di definire `direction`, `type` e un valore univoco `name` nel file function.json. Il modo in cui si definiscono questi attributi dipende dal linguaggio dell'app per le funzioni.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Quando si usa un binding di output, non è necessario usare il codice di Azure Storage SDK per l'autenticazione, per recuperare un riferimento alla coda o per scrivere dati. Queste attività vengono eseguite automaticamente dal runtime di Funzioni e dal binding di output.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Come prima, usare il comando seguente per avviare il runtime di Funzioni in locale:

```bash
func host start
```

> [!NOTE]  
> Poiché sono stati abilitati i bundle di estensioni nel file host.json, durante l'avvio è stata scaricata e configurata l'[estensione di binding di archiviazione](functions-bindings-storage-blob.md#packages---functions-2x), nonché altre estensioni di binding di Microsoft.

Copiare l'URL della funzione `HttpTrigger` dall'output del runtime e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` a questo URL ed eseguire la richiesta. Nel browser dovrebbe essere visualizzata la stessa risposta dell'articolo precedente.

Questa volta il binding di output crea anche una coda denominata `outqueue` nell'account di archiviazione e aggiunge un messaggio con questa stessa stringa.

Quindi, usare l'interfaccia della riga di comando di Azure per visualizzare la nuova coda e verificare che è stato aggiunto un messaggio. È anche possibile visualizzare la coda usando [Microsoft Azure Storage Explorer][Azure Storage Explorer] oppure nel [portale di Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Ridistribuire il progetto 

Per aggiornare l'app pubblicata, usare il comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) di Core Tools per distribuire il codice del progetto in Azure. In questo esempio sostituire `<APP_NAME>` con il nome dell'app.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Anche in questo caso, è possibile usare cURL o un browser per testare la funzione distribuita. Come prima, aggiungere la stringa di query `&name=<yourname>` all'URL, come indicato in questo esempio:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

È possibile [esaminare di nuovo il messaggio della coda di archiviazione](#query-the-storage-queue) per verificare che il binding di output genera un nuovo messaggio nella coda, come previsto.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

La funzione di trigger HTTP è stata aggiornata per scrivere dati in una coda di archiviazione. Per altre informazioni sullo sviluppo di Funzioni di Azure con Python, vedere [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md) e [Trigger e binding di Funzioni di Azure](functions-triggers-bindings.md). Per alcuni esempi di progetti di Funzioni completi in Python, vedere gli [esempi di funzioni Python](/samples/browse/?products=azure-functions&languages=python). Per altre informazioni sui prezzi, vedere la pagina [Prezzi di Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/) e l'articolo [Estimating Consumption plan costs](functions-consumption-costs.md) (Stima dei costi del piano a consumo).

Quindi, è consigliabile abilitare il monitoraggio di Application Insights per l'app per le funzioni:

> [!div class="nextstepaction"]
> [Abilitare l'integrazione di Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/