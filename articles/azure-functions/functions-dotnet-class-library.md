---
title: Guida di riferimento per gli sviluppatori C# di Funzioni di Azure
description: Informazioni su come sviluppare Funzioni di Azure in C#.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: 9ecaff438eb30e6a05a7e744e17d2d713eef21cd
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665577"
---
# <a name="azure-functions-c-developer-reference"></a>Guida di riferimento per gli sviluppatori C# di Funzioni di Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure tramite C# nelle librerie di classi .NET.

Funzioni di Azure supporta i linguaggi di programmazione C# e script C#. Per materiale sussidiario sull'[uso di C# nel portale di Azure](functions-create-function-app-portal.md), vedere [Guida di riferimento a per sviluppatori di script C# (.csx)](functions-reference-csharp.md).

Questo articolo presuppone che l'utente abbia già letto gli articoli seguenti:

* [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md)
* [Strumenti di Visual Studio 2019 per funzioni di Azure](functions-develop-vs.md)

## <a name="supported-versions"></a>Versioni supportate

Il runtime di funzioni di Azure 2. x USA .NET Core 2,2. Il codice di funzione può usare le API di .NET Core 2,2 aggiornando le impostazioni del progetto di Visual Studio. I modelli di funzione non usano per impostazione predefinita .NET Core 2,2 per evitare che i clienti che non dispongono di .NET Core 2,2 siano installati in modo negativo.

## <a name="functions-class-library-project"></a>Progetto di libreria di classi per Funzioni

In Visual Studio il modello di progetto **Funzioni di Azure** crea un progetto di libreria di classi C# contenente i file seguenti:

* [host.json](functions-host-json.md): archivia le impostazioni di configurazione che interessano tutte le funzioni del progetto quando vengono eseguite nell'ambiente locale o in Azure.
* [local.settings.json](functions-run-local.md#local-settings-file): archivia le impostazioni dell'app e le stringhe di connessione usate per l'esecuzione nell'ambiente locale. Questo file contiene segreti e non viene pubblicato nell'app per le funzioni in Azure. Aggiungere invece [le impostazioni dell'app all'app per le funzioni](functions-develop-vs.md#function-app-settings).

Quando si compila il progetto, nella directory di output di compilazione viene generata una struttura di cartelle simile all'esempio seguente:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Questa directory viene distribuita all'app per le funzioni in Azure. Le estensioni di associazione necessarie nella [versione 2.x](functions-versions.md) del runtime di Funzioni vengono [aggiunte al progetto come pacchetti NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Il processo di compilazione crea un file *function.json* per ogni funzione. Il file *function.json* non viene modificato direttamente. Non è possibile modificare la configurazione di associazione o disabilitare la funzione modificando il file. Per informazioni su come disabilitare una funzione, vedere [Come disabilitare le funzioni](disable-function.md#functions-2x---c-class-libraries).


## <a name="methods-recognized-as-functions"></a>Metodi riconosciuti come funzioni

In una libreria di classi, una funzione è un metodo statico con un attributo `FunctionName` e trigger, come illustrato nell'esempio seguente:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

L'attributo `FunctionName` indica il metodo come punto di ingresso della funzione. Il nome deve essere univoco all'interno di un progetto, iniziare con una lettera e contenere solo lettere, numeri, `_`e `-`, con una lunghezza di 127 caratteri. I modelli di progetto spesso creano un metodo denominato `Run`, ma il nome del metodo può essere qualsiasi nome di metodo c# valido.

L'attributo trigger specifica il tipo di trigger e associa i dati di input a un parametro del metodo. La funzione di esempio viene attivata da un messaggio della coda e il messaggio della coda viene passato al metodo nel parametro `myQueueItem`.

## <a name="method-signature-parameters"></a>Parametri di Method Signature

Method Signature può contenere parametri diversi da quelli usati con l'attributo trigger. Ecco alcuni dei parametri aggiuntivi che è possibile includere:

* [Associazioni di input e output](functions-triggers-bindings.md) contrassegnate come tali tramite la decorazione con attributi.  
* Un parametro `ILogger` o `TraceWriter` ([solo versione 1.x](functions-versions.md#creating-1x-apps)) per la [registrazione](#logging).
* Un parametro `CancellationToken` per [arresto normale](#cancellation-tokens).
* Parametri di [espressioni di associazione](./functions-bindings-expressions-patterns.md) per ottenere i metadati trigger.

L'ordine dei parametri nella firma della funzione non è rilevante. È ad esempio possibile inserire i parametri di trigger prima o dopo le altre associazioni e il parametro del logger prima o dopo i parametri di associazione o di trigger.

### <a name="output-binding-example"></a>Esempio di associazione di output

L'esempio seguente differisce dal precedente per l'aggiunta di un'associazione di coda di output. La funzione scrive il messaggio della coda che attiva la funzione in un nuovo messaggio di un'altra coda.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Gli articoli di riferimento di associazione ([Code di archiviazione](functions-bindings-storage-queue.md), ad esempio) illustrano quali tipi di parametri è possibile usare con attributi di associazione di trigger, input o output.

### <a name="binding-expressions-example"></a>Esempio di espressioni di associazione

Il codice seguente ottiene il nome della coda per il monitoraggio da un'impostazione di app e l'ora di creazione del messaggio della coda nel parametro `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Function.json generato automaticamente

Il processo di compilazione crea un file *function.json* in una cartella della funzione nella cartella di compilazione. Come affermato in precedenza, questo file non viene modificato direttamente. Non è possibile modificare la configurazione di associazione o disabilitare la funzione modificando il file. 

Lo scopo di questo file è fornire informazioni che il controller di scalabilità userà per [decisioni di scalabilità nel piano a consumo](functions-scale.md#how-the-consumption-and-premium-plans-work). Per questo motivo il file contiene solo informazioni di trigger, non associazioni di input o output.

Il file *function.json* generato include una proprietà `configurationSource` che indica al runtime di usare gli attributi .NET per le associazioni invece della configurazione *function.json*. Ecco un esempio:

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

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

La generazione del file *function.json* viene eseguita dal pacchetto NuGet [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Per le versioni 1.x e 2.x del runtime di Funzioni viene usato lo stesso pacchetto. Un progetto 1.x si distingue da un progetto 2.x in base al framework di destinazione. Ecco le parti rilevanti dei file con estensione *csproj* che mostrano i diversi framework di destinazione e lo stesso pacchetto `Sdk`:

**Funzioni 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Funzioni 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Tra le dipendenze del pacchetto `Sdk` sono inclusi i trigger e le associazioni. Un progetto 1. x fa riferimento a trigger e binding 1. x perché i trigger e le associazioni sono destinati alla .NET Framework, mentre i trigger e le associazioni 2. x hanno come destinazione .NET Core.

Il pacchetto `Sdk` dipende inoltre da [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) e indirettamente da [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Queste dipendenze consentono di assicurarsi che il progetto usi le versioni dei pacchetti compatibili con la versione del runtime di Funzioni definita come destinazione del progetto. Se ad esempio è disponibile `Newtonsoft.Json` versione 11 per .NET Framework 4.6.1, ma il runtime di Funzioni che ha come destinazione .NET Framework 4.6.1 è compatibile solo con `Newtonsoft.Json` 9.0.1, anche il codice delle funzioni nel progetto deve usare `Newtonsoft.Json` 9.0.1.

Il codice sorgente per `Microsoft.NET.Sdk.Functions` è disponibile nel repository GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versione del runtime

Per eseguire i progetti di Funzioni, Visual Studio usa gli [strumenti di base di Funzioni di Azure](functions-run-local.md#install-the-azure-functions-core-tools). Gli strumenti di base offrono un'interfaccia della riga di comando per il runtime di Funzioni.

Se si installano gli strumenti di base usando npm, questo non ha alcun effetto sulla versione degli strumenti di base usata da Visual Studio. Per la versione 1.x del runtime di Funzioni, Visual Studio archivia le versioni degli strumenti di base in *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* e usa la versione più recente archiviata. Per Funzioni 2.x, gli strumenti di base sono inclusi nell'estensione **Funzioni di Azure e strumenti per processi Web**. Per entrambe le versioni 1.x e 2.x, è possibile vedere la versione in uso nell'output della console quando si esegue un progetto di Funzioni:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Tipi supportati per le associazioni

Ogni associazione supporta determinati tipi. Ad esempio è possibile applicare un attributo trigger di BLOB a un parametro stringa, un parametro POCO, un parametro `CloudBlockBlob` o uno dei molti altri tipi supportati. L'[articolo di riferimento sull'associazione relativo alle associazioni BLOB](functions-bindings-storage-blob.md#trigger---usage) elenca tutti i tipi di parametri supportati. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md) e i [documenti di riferimento per ogni tipo di associazione](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Associazione al valore restituito dal metodo

È possibile usare un valore restituito dal metodo per un'associazione di output applicando l'attributo a tale valore. Per alcuni esempi, vedere [Trigger e associazioni](./functions-bindings-return-value.md). 

Usare il valore restituito solo se la corretta esecuzione di una funzione restituisce sempre un valore da passare all'associazione di output. In caso contrario, usare `ICollector` o `IAsyncCollector`, come illustrato nella sezione seguente.

## <a name="writing-multiple-output-values"></a>Scrittura di più valori di output

Per scrivere più valori in un'associazione di output o se una chiamata corretta alla funzione potrebbe non restituire alcun valore da passare all'associazione di output, usare i tipi [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Questi tipi sono raccolte di sola scrittura che vengono scritte nell'associazione di output durante il completamento del metodo.

Questo esempio scrive più messaggi in coda nella stessa coda usando `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Registrazione

Per registrare l'output nei log in streaming in C#, includere un argomento di tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Si consiglia di denominarlo `log`, come nell'esempio seguente:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Evitare di usare `Console.Write` in Funzioni di Azure. Per altre informazioni, vedere [Scrivere log in funzioni C#](functions-monitoring.md#write-logs-in-c-functions) nell'articolo **Monitorare Funzioni di Azure**.

## <a name="async"></a>Async

Per rendere una funzione [asincrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), usare la parola chiave `async` e restituire un oggetto `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Non è possibile usare i parametri `out` in funzioni asincrone. Per le associazioni di output, usare invece il [valore restituito di funzione](#binding-to-method-return-value) o un [oggetto agente di raccolta](#writing-multiple-output-values).

## <a name="cancellation-tokens"></a>Token di annullamento

Una funzione può accettare un parametro [CancellationToken](/dotnet/api/system.threading.cancellationtoken), che consente al sistema operativo di notificare il codice quando la funzione sta per essere terminata. È possibile usare questa notifica per assicurarsi che la funzione non termini in modo imprevisto lasciando i dati in uno stato incoerente.

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
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Le impostazioni dell'app possono essere lette dalle variabili di ambiente durante lo sviluppo in locale e durante l'esecuzione in Azure. Durante lo sviluppo in locale, le impostazioni dell'app vengono ricavate dalla raccolta `Values` nel file *local.settings.json*. In entrambi gli ambienti, locale e Azure, `GetEnvironmentVariable("<app setting name>")` recupera il valore dell'impostazione dell'app denominata. In caso di esecuzione locale, ad esempio, se il file *local.settings.json* contiene `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`, viene restituito "My Site Name".

La proprietà [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) è un'API alternativa per il recupero di valori di impostazione dell'app, ma è consigliabile usare `GetEnvironmentVariable` come illustrato di seguito.

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

  `BindingTypeAttribute` è l'attributo .NET che definisce l'associazione e `T` è un tipo di input o output supportato da quel tipo di associazione. `T` non può essere un tipo di parametro `out`, ad esempio `out JObject`. Ad esempio, l'associazione di output della tabella app per dispositivi mobili supporta [sei tipi di output](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ma è possibile usare solo [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) con binding imperativo.

### <a name="single-attribute-example"></a>Esempio con un solo attributo

L'esempio di codice seguente crea un [associazione di output del BLOB di archiviazione](functions-bindings-storage-blob.md#output) con percorso del BLOB definito in fase di esecuzione, quindi scrive una stringa per il BLOB.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definisce l'associazione di input o output del [BLOB di archiviazione](functions-bindings-storage-blob.md) e [TextWriter](/dotnet/api/system.io.textwriter) è un tipo di associazione di output supportato.

### <a name="multiple-attribute-example"></a>Esempio con più attributi

L'esempio precedente ottiene l'impostazione dell'app per la stringa di connessione dell'account di archiviazione principale dell'app, ovvero `AzureWebJobsStorage`. È possibile specificare un'impostazione app personalizzata da usare per l'account di archiviazione aggiungendo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando la matrice di attributi in `BindAsync<T>()`. Usare un parametro `Binder` e non `IBinder`.  ad esempio:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
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

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui trigger e le associazioni](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Altre informazioni sulle procedure consigliate per Funzioni di Azure](functions-best-practices.md)
