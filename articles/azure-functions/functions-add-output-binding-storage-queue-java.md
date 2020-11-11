---
title: Connettere la funzione Java ad archiviazione di Azure
description: Informazioni su come connettere una funzione Java attivata da HTTP ad archiviazione di Azure usando il binding dell'output di archiviazione code.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422655"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Connettere la funzione Java ad archiviazione di Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Questo articolo illustra come integrare la funzione creata nel [precedente articolo di avvio rapido](./create-first-function-cli-java.md?tabs=bash,browser) con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

La maggior parte dei binding richiede una stringa di connessione archiviata che verrà usata da Funzioni per accedere al servizio associato. Per semplificare questa connessione, usare l'account di archiviazione creato con l'app per le funzioni. La connessione a questo account è già archiviata in un'impostazione dell'app denominata `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare con questo articolo, completare i passaggi della [parte 1 dell'argomento di avvio rapido su Java](./create-first-function-cli-java.md?tabs=bash,browser).

## <a name="download-the-function-app-settings"></a>Scaricare le impostazioni dell'app per le funzioni

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

È ora possibile aggiungere il binding di output di archiviazione nel progetto.

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

È ora possibile provare il nuovo binding di output in locale.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Come prima, usare il comando seguente per creare il progetto e avviare il runtime di Funzioni in locale:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Poiché sono stati abilitati i bundle di estensioni nel file host.json, durante l'avvio è stata scaricata e configurata l'[estensione di binding di archiviazione](functions-bindings-storage-blob.md#add-to-your-functions-app), nonché altre estensioni di binding di Microsoft.

Come prima, attivare la funzione dalla riga di comando usando cURL in una nuova finestra del terminale:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Questa volta il binding di output crea anche una coda denominata `outqueue` nell'account di archiviazione e aggiunge un messaggio con questa stessa stringa.

Quindi, usare l'interfaccia della riga di comando di Azure per visualizzare la nuova coda e verificare che è stato aggiunto un messaggio. È anche possibile visualizzare la coda usando [Microsoft Azure Storage Explorer][Azure Storage Explorer] oppure nel [portale di Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Ridistribuire il progetto 

Per aggiornare l'app pubblicata, eseguire di nuovo il comando seguente:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Anche in questo caso, è possibile usare cURL per testare la funzione distribuita. Come prima, passare il valore `AzureFunctions` nel corpo della richiesta POST all'URL, come indicato in questo esempio:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

È possibile [esaminare di nuovo il messaggio della coda di archiviazione](#query-the-storage-queue) per verificare che il binding di output genera un nuovo messaggio nella coda, come previsto.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

La funzione di trigger HTTP è stata aggiornata per scrivere dati in una coda di archiviazione. Per altre informazioni sullo sviluppo di Funzioni di Azure con Java, vedere [Guida per sviluppatori Java per Funzioni di Azure](functions-reference-java.md) e [Trigger e binding di Funzioni di Azure](functions-triggers-bindings.md). Per alcuni esempi di progetti di Funzioni completi in Java, vedere gli [esempi di funzioni Java](/samples/browse/?products=azure-functions&languages=Java). 

Quindi, è consigliabile abilitare il monitoraggio di Application Insights per l'app per le funzioni:

> [!div class="nextstepaction"]
> [Abilitare l'integrazione di Application Insights](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
