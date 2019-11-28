---
title: Connettere la funzione Java ad archiviazione di Azure
description: Informazioni su come connettere una funzione Java attivata da HTTP ad archiviazione di Azure usando il binding dell'output di archiviazione code.
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: f9c3445efbe14c27cad9de62f5b46f50965e3145
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231216"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Connettere la funzione Java ad archiviazione di Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Questo articolo illustra come integrare la funzione creata nel [precedente articolo di avvio rapido](functions-create-first-java-maven.md) con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

La maggior parte dei binding richiede una stringa di connessione archiviata che verrà usata da Funzioni per accedere al servizio associato. Per semplificare questa connessione, usare l'account di archiviazione creato con l'app per le funzioni. La connessione a questo account è già archiviata in un'impostazione dell'app denominata `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare con questo articolo, completare i passaggi della [parte 1 dell'argomento di avvio rapido su Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Scaricare le impostazioni dell'app per le funzioni

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

È ora possibile aggiungere il binding di output di archiviazione nel progetto.

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

In un progetto Java i binding vengono definiti come annotazioni di binding nel metodo della funzione. Il file *function.json* viene quindi generato automaticamente in base a queste annotazioni.

Passare al percorso del codice della funzione, _src/main/java_, aprire il file di progetto *Function.java* e aggiungere il parametro seguente alla definizione del metodo `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Il parametro `msg` è un tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), che rappresenta una raccolta di stringhe scritte come messaggi in un binding di output al completamento della funzione. In questo caso, l'output è una coda di archiviazione denominata `outqueue`. La stringa di connessione per l'account di archiviazione è impostata dal metodo `connection`. Invece della stringa di connessione stessa, passare l'impostazione applicazione che contiene la stringa di connessione dell'account di archiviazione.

La definizione del metodo `run` dovrà ora essere come indicato nell'esempio seguente:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

Ora è possibile usare il nuovo parametro `msg` per scrivere nel binding di output dal codice della funzione. Aggiungere la riga di codice seguente prima della risposta di operazione riuscita per aggiungere il valore di `name` al binding di output `msg`.

```java
msg.setValue(name);
```

Quando si usa un binding di output, non è necessario usare il codice di Azure Storage SDK per l'autenticazione, per recuperare un riferimento alla coda o per scrivere dati. Queste attività vengono eseguite automaticamente dal runtime di Funzioni e dal binding di output.

Il metodo `run` dovrà ora essere come indicato nell'esempio seguente:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Aggiornare i test

Poiché l'archetipo crea anche un set di test, è necessario aggiornare questi test per gestire il nuovo parametro `msg` nella firma del metodo `run`.  

Passare al percorso del codice di test, _src/test/java_, aprire il file di progetto *Function.java* e sostituire la riga di codice sotto `//Invoke` con il codice seguente.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

È ora possibile provare il nuovo binding di output in locale.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Come prima, usare il comando seguente per creare il progetto e avviare il runtime di Funzioni in locale:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Poiché sono stati abilitati i bundle di estensioni nel file host.json, durante l'avvio è stata scaricata e configurata l'[estensione di binding di archiviazione](functions-bindings-storage-blob.md#packages---functions-2x), nonché altre estensioni di binding di Microsoft.

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

```azurecli
mvn azure-functions:deploy
```

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
> [Abilitare l'integrazione di Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/