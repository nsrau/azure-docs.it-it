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
ms.openlocfilehash: 8bd46adc475af35d32b9e329a3765e064120a6e3
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-c-developer-reference"></a>Guida di riferimento per gli sviluppatori C# di Funzioni di Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

In questo articolo viene fornita un'introduzione allo sviluppo di funzioni di Azure tramite c# nelle librerie di classi .NET.

Funzioni di Azure supporta i linguaggi c# e script c# linguaggi di programmazione. Se si sta cercando informazioni aggiuntive [utilizzando c# nel portale di Azure](functions-create-function-app-portal.md), vedere [riferimenti per sviluppatori c# script (con estensione csx)](functions-reference-csharp.md).

Questo articolo si presuppone che sia già stata letta gli articoli seguenti:

* [Guida per gli sviluppatori di Azure funzioni](functions-reference.md)
* [Strumenti di Visual Studio 2017 funzioni di Azure](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Progetto di libreria di classi per Funzioni

In Visual Studio, il **Azure funzioni** modello di progetto crea un c# progetto libreria di classi che contiene i file seguenti:

* [host.JSON](functions-host-json.md) -archivia le impostazioni di configurazione che influiscono su tutte le funzioni nel progetto quando in esecuzione in locale o in Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -consente di archiviare le impostazioni dell'app e le stringhe di connessione vengono utilizzate durante l'esecuzione in locale.

### <a name="functionname-and-trigger-attributes"></a>Attributi FunctionName e trigger

In una libreria di classi, una funzione è un metodo statico con un `FunctionName` e un attributo di trigger, come illustrato nell'esempio seguente:

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

Il `FunctionName` come un punto di ingresso della funzione al metodo contrassegnato dall'attributo. Il nome deve essere univoco all'interno di un progetto.

L'attributo trigger specifica il tipo di trigger e associa i dati di input a un parametro del metodo. La funzione di esempio viene attivata da un messaggio nella coda e il messaggio della coda viene passato al metodo nel `myQueueItem` parametro.

### <a name="additional-binding-attributes"></a>Attributi di associazione aggiuntivi

Ulteriori input e output attributi di associazione possono essere utilizzati. Nell'esempio seguente viene modificato il precedente tramite l'aggiunta di un'associazione di coda di output. La funzione scrive il messaggio della coda di input in un nuovo messaggio nella coda in un'altra coda.

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

### <a name="conversion-to-functionjson"></a>Conversione in function.json

Il processo di compilazione crea un *function.json* file in una cartella di funzione nella cartella di compilazione. Questo file non deve essere modificato direttamente. È possibile modificare la configurazione di associazione o disabilitare la funzione modificando il file. 

Lo scopo di questo file consiste nel fornire informazioni per il controller di scala da usare per [l'aumento delle decisioni il piano di consumo](functions-scale.md#how-the-consumption-plan-works). Per questo motivo, il file contiene solo informazioni di trigger, non di input o output associazioni.

Generato *function.json* file include un `configurationSource` proprietà che indica al runtime di utilizzare attributi .NET per le associazioni, invece di *function.json* configurazione. Ad esempio:

{"generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0", "configurationSource": "attributi", "binding": [{"type": "queueTrigger", "queueName": "% coda di input %", "name": "myQueueItem"}], "disabilitato": false, "scriptFile": "... \\bin\\FunctionApp1.dll ","punto di ingresso":"FunctionApp1.QueueTrigger.Run"}

Il *function.json* viene eseguita la generazione di file dal pacchetto NuGet di [Microsoft\.NET\.Sdk\.funzioni](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). Il codice sorgente è disponibile nel repository GitHub [azure\-funzioni\-vs\-compilare\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="supported-types-for-bindings"></a>Tipi supportati per le associazioni

Ogni associazione dispone di propri tipi supportati. ad esempio, un attributo di trigger di blob può essere applicato a un parametro di stringa, un parametro POCO, un `CloudBlockBlob` parametro oppure uno dei molti altri tipi supportati. Il [articolo di riferimento dell'associazione per le associazioni di blob](functions-bindings-storage-blob.md#trigger---usage) supportati Elenca tutti i tipi di parametro. Per ulteriori informazioni, vedere [trigger e le associazioni](functions-triggers-bindings.md) e [documenti di riferimento dell'associazione per ogni tipo di associazione](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Associazione al valore restituito (metodo)

È possibile utilizzare un valore restituito del metodo per un'associazione di output, come illustrato nell'esempio seguente:

```csharp
public static class ReturnValueOutputBinding
{
    [FunctionName("CopyQueueMessageUsingReturnValue")]
    [return: Queue("myqueue-items-destination")]
    public static string Run(
        [QueueTrigger("myqueue-items-source-2")] string myQueueItem,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        return myQueueItem;
    }
}
```

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
> Per informazioni su un framework di registrazione più recenti che è possibile utilizzare invece di `TraceWriter`, vedere [scrittura registra in c# le funzioni](functions-monitoring.md#write-logs-in-c-functions) nel **monitoraggio Azure funzioni** articolo.

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

Alcune operazioni richiedono l'arresto normale. Anche se è sempre consigliabile scrivere il codice in grado di gestire un arresto anomalo, nei casi in cui si desidera gestire le richieste di arresto, definire un [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) digitato l'argomento.  È fornito un `CancellationToken` per segnalare che viene avviato un arresto dell'host.

```csharp
public static class CancellationTokenExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
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

In c# e altri linguaggi .NET, è possibile utilizzare un [imperativo](https://en.wikipedia.org/wiki/Imperative_programming) associazione modello, anziché il [ *dichiarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) associazioni di attributi. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Con questo modello, è possibile associare a supportati input e output associazioni il volo nel codice di funzione.

Definire un'associazione imperativa, come segue:

- **Non** includere un attributo nella firma della funzione per i binding imperativi desiderati.
- Passare un parametro di input [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Usare il seguente modello C# per eseguire l'associazione dati.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`è l'attributo .NET che definisce l'associazione, e `T` è un tipo di input o output che è supportato dal tipo di associazione. `T`non può essere un `out` tipo di parametro (ad esempio `out JObject`). Ad esempio, la tabella di App per dispositivi mobili output supporta l'associazione [sei tipi di output](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ma è possibile utilizzare solo [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)con associazione imperativo.

### <a name="single-attribute-example"></a>Esempio di attributo singolo

L'esempio di codice seguente crea un [associazione di output del BLOB di archiviazione](functions-bindings-storage-blob.md#input--output) con percorso del BLOB definito in fase di esecuzione, quindi scrive una stringa per il BLOB.

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

### <a name="multiple-attribute-example"></a>Esempio di attributo multipli

Nell'esempio precedente si ottiene l'impostazione dell'app per la stringa di connessione di funzione dell'applicazione principale Storage account (ovvero `AzureWebJobsStorage`). È possibile specificare un'impostazione app personalizzata da usare per l'account di archiviazione aggiungendo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando la matrice di attributi in `BindAsync<T>()`. Utilizzare un `Binder` parametro, non `IBinder`.  Ad esempio: 

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

Nella tabella seguente sono elencati gli attributi di associazione che sono disponibili in un progetto libreria di classi di funzioni di Azure e il trigger. Tutti gli attributi sono contenuti nello spazio dei nomi `Microsoft.Azure.WebJobs`.

| Trigger | Input | Output|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [BLOB](functions-bindings-storage-blob.md#input--output---attributes)| [BLOB](functions-bindings-storage-blob.md#input--output---attributes)|
| [CosmosDBTrigger](functions-bindings-documentdb.md#trigger---attributes)| [DocumentDB](functions-bindings-documentdb.md#input---attributes)| [DocumentDB](functions-bindings-documentdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [Hub eventi](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Coda](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [Bus di servizio](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Tabella](functions-bindings-storage-table.md#input---attributes)| [Tabella](functions-bindings-storage-table.md#output---attributes)  | 
| ||[Hub di notifica](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ulteriori informazioni sui trigger e associazioni](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Altre informazioni sulle procedure consigliate per le funzioni di Azure](functions-best-practices.md)
