---
title: Guida di riferimento per gli sviluppatori C# di Funzioni di Azure
description: Informazioni su come sviluppare Funzioni di Azure in C#.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 9e9aa8a36d363ce28d61c5ba3cfe758520a626cf
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="azure-functions-c-developer-reference"></a>Guida di riferimento per gli sviluppatori C# di Funzioni di Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure tramite C# nelle librerie di classi .NET.

Funzioni di Azure supporta i linguaggi di programmazione C# e script C#. Per materiale sussidiario sull'[uso di C# nel portale di Azure](functions-create-function-app-portal.md), vedere [Guida di riferimento a per sviluppatori di script C# (.csx)](functions-reference-csharp.md).

Questo articolo presuppone che l'utente abbia già letto gli articoli seguenti:

* [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md)
* [Strumenti di Visual Studio 2017 per Funzioni di Azure](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Progetto di libreria di classi per Funzioni

In Visual Studio il modello di progetto **Funzioni di Azure** crea un progetto di libreria di classi C# contenente i file seguenti:

* [host.json](functions-host-json.md): archivia le impostazioni di configurazione che interessano tutte le funzioni del progetto quando vengono eseguite nell'ambiente locale o in Azure.
* [local.settings.json](functions-run-local.md#local-settings-file): archivia le impostazioni dell'app e le stringhe di connessione usate per l'esecuzione nell'ambiente locale.

> [!IMPORTANT]
> Il processo di compilazione crea un file *function.json* per ogni funzione. Il file *function.json* non viene modificato direttamente. Non è possibile modificare la configurazione di associazione o disabilitare la funzione modificando il file. Per disabilitare una funzione, usare l'attributo il [Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs). Aggiungere ad esempio all'app l'impostazione booleana MY_TIMER_DISABLED e applicare `[Disable("MY_TIMER_DISABLED")]` alla funzione. Sarà quindi possibile abilitarla e disabilitarla modificando l'impostazione dell'app.

### <a name="functionname-and-trigger-attributes"></a>Attributi FunctionName e trigger

In una libreria di classi, una funzione è un metodo statico con un attributo `FunctionName` e trigger, come illustrato nell'esempio seguente:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

L'attributo `FunctionName` indica il metodo come punto di ingresso della funzione. Il nome deve essere univoco nel progetto.

L'attributo trigger specifica il tipo di trigger e associa i dati di input a un parametro del metodo. La funzione di esempio viene attivata da un messaggio della coda e il messaggio della coda viene passato al metodo nel parametro `myQueueItem`.

### <a name="additional-binding-attributes"></a>Altri attributi di associazione

È possibile usare altri attributi di associazione di input e output. L'esempio seguente differisce dal precedente per l'aggiunta di un'associazione di coda di output. La funzione scrive il messaggio della coda di input in un nuovo messaggio di un'altra coda.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

### <a name="order-of-parameters"></a>Ordine dei parametri

L'ordine dei parametri nella firma della funzione non è rilevante. È ad esempio possibile inserire i parametri di trigger prima o dopo le altre associazioni e il parametro del logger prima o dopo i parametri di associazione o di trigger.

### <a name="binding-expressions"></a>Espressioni di associazione

È possibile usare le espressioni di associazione nei parametri di un costruttore di attributo e in quelli di una funzione. Il codice seguente, ad esempio, ottiene il nome della coda per il monitoraggio da un'impostazione di app e l'ora di creazione del messaggio della coda nel parametro `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

Per altre informazioni, vedere **Modelli ed espressioni di associazione** in [Trigger e associazioni](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="conversion-to-functionjson"></a>Conversione in function.json

Il processo di compilazione crea un file *function.json* in una cartella della funzione nella cartella di compilazione. Come affermato in precedenza, questo file non viene modificato direttamente. Non è possibile modificare la configurazione di associazione o disabilitare la funzione modificando il file. 

Lo scopo di questo file è fornire informazioni che il controller di scalabilità userà per [decisioni di scalabilità nel piano a consumo](functions-scale.md#how-the-consumption-plan-works). Per questo motivo il file contiene solo informazioni di trigger, non associazioni di input o output.

Il file *function.json* generato include una proprietà `configurationSource` che indica al runtime di usare gli attributi .NET per le associazioni invece della configurazione *function.json*. Ad esempio:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

La generazione del file *function.json* viene eseguita dal pacchetto NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). Il codice sorgente è disponibile nel repository GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="supported-types-for-bindings"></a>Tipi supportati per le associazioni

Ogni associazione supporta determinati tipi. Ad esempio è possibile applicare un attributo trigger di BLOB a un parametro stringa, un parametro POCO, un parametro `CloudBlockBlob` o uno dei molti altri tipi supportati. L'[articolo di riferimento sull'associazione relativo alle associazioni BLOB](functions-bindings-storage-blob.md#trigger---usage) elenca tutti i tipi di parametri supportati. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md) e i [documenti di riferimento per ogni tipo di associazione](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Associazione al valore restituito dal metodo

È possibile usare un valore restituito dal metodo per un'associazione di output applicando l'attributo a tale valore. Per alcuni esempi, vedere [Trigger e associazioni](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Scrittura di più valori di output

Per scrivere più valori in un'associazione di output, usare i tipi [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Questi tipi sono raccolte di sola scrittura che vengono scritte nell'associazione di output durante il completamento del metodo.

Questo esempio scrive più messaggi in coda nella stessa coda usando `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Registrazione

Per registrare l'output nei log in streaming in C#, includere un argomento di tipo `TraceWriter`. È consigliabile denominarlo `log`. Evitare di usare `Console.Write` in Funzioni di Azure. 

`TraceWriter` è definito in [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Il livello di registrazione per `TraceWriter` può essere configurato in [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Per informazioni su un framework di registrazione più recente che è possibile usare al posto di `TraceWriter`, vedere [Scrivere i log nelle funzioni C#](functions-monitoring.md#write-logs-in-c-functions) nell'articolo **Monitorare Funzioni di Azure**.

## <a name="async"></a>Async

Per rendere una funzione asincrona, usare la parola chiave `async` e restituire un oggetto `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Token di annullamento

Una funzione può accettare un parametro [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx), che consente al sistema operativo di notificare il codice quando la funzione sta per essere terminata. È possibile usare questa notifica per assicurarsi che la funzione non termini in modo imprevisto lasciando i dati in uno stato incoerente.

L'esempio seguente illustra come verificare l'eventuale imminente interruzione della funzione.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Variabili di ambiente

Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `System.Environment.GetEnvironmentVariable`come illustrato nell'esempio di codice seguente:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

## <a name="binding-at-runtime"></a>Associazione in fase di esecuzione

In C# e altri linguaggi .NET, è possibile usare un modello di associazione [imperativo](https://en.wikipedia.org/wiki/Imperative_programming) anziché [*dichiarativo*](https://en.wikipedia.org/wiki/Declarative_programming) negli attributi. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Con questo modello è possibile associare rapidamente i dati ad associazioni di input e output supportate nel codice della funzione.

Definire un'associazione imperativa, come segue:

- **Non** includere un attributo nella firma della funzione per le associazioni imperative.
- Passare un parametro di input [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Usare il seguente modello C# per eseguire l'associazione dati.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` è l'attributo .NET che definisce l'associazione e `T` è un tipo di input o output supportato da quel tipo di associazione. `T` non può essere un tipo di parametro `out`, ad esempio `out JObject`. L'associazione di output della tabella App per dispositivi mobili supporta ad esempio [sei tipi di output](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ma è possibile usare solo [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) con l'associazione imperativa.

### <a name="single-attribute-example"></a>Esempio con un solo attributo

L'esempio di codice seguente crea un [associazione di output del BLOB di archiviazione](functions-bindings-storage-blob.md#output) con percorso del BLOB definito in fase di esecuzione, quindi scrive una stringa per il BLOB.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definisce l'associazione di input o output del [BLOB di archiviazione](functions-bindings-storage-blob.md) e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) è un tipo di associazione di output supportato.

### <a name="multiple-attribute-example"></a>Esempio con più attributi

L'esempio precedente ottiene l'impostazione dell'app per la stringa di connessione dell'account di archiviazione principale dell'app, ovvero `AzureWebJobsStorage`. È possibile specificare un'impostazione app personalizzata da usare per l'account di archiviazione aggiungendo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando la matrice di attributi in `BindAsync<T>()`. Usare un parametro `Binder` e non `IBinder`.  Ad esempio: 

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Trigger e associazioni 

La tabella seguente elenca i trigger e gli attributi di associazione disponibili in un progetto di libreria di classi di Funzioni di Azure. Tutti gli attributi sono contenuti nello spazio dei nomi `Microsoft.Azure.WebJobs`.

| Trigger | Input | Output|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [BLOB](functions-bindings-storage-blob.md#input---attributes)| [BLOB](functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Coda](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [Bus di servizio](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Tabella](functions-bindings-storage-table.md#input---attributes)| [Tabella](functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui trigger e le associazioni](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Altre informazioni sulle procedure consigliate per Funzioni di Azure](functions-best-practices.md)
