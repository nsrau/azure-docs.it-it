---
title: Guida di riferimento a Funzioni di Azure per sviluppatori di script C#
description: Informazioni su come sviluppare Funzioni di Azure usando script C#.
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
ms.openlocfilehash: 5a4fc57606b0cf09f8d20710e3c83637283014ba
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Guida di riferimento a Funzioni di Azure per sviluppatori di script C# (.csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Questo articolo riporta un'introduzione allo sviluppo di Funzioni di Azure tramite script C# (*.csx*).

Funzioni di Azure supporta i linguaggi di programmazione C# e script C# . Per materiale sussidiario sull'[uso di C# in un progetto di libreria di classi di Visual Studio](functions-develop-vs.md), vedere [Informazioni di riferimento per sviluppatori C#](functions-dotnet-class-library.md).

Questo articolo presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md).

## <a name="how-csx-works"></a>Funzionamento di CSX

L'esperienza con gli script C# per Funzioni di Azure si basa su [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). I dati vengono trasmessi alla funzione C# tramite argomenti del metodo. I nomi di argomento sono specificati in un file `function.json` e sono disponibili nomi predefiniti per l'accesso a elementi quali il logger delle funzioni e i token di annullamento.

Il formato *.csx* consente di scrivere meno "boilerplate" e di concentrarsi solo sulla scrittura della funzione C#. Anziché eseguire il wrapping di tutti gli elementi in un spazio dei nomi e classe, definire semplicemente un metodo `Run`. Includere tutti i riferimenti ad assembly e gli spazi dei nomi all'inizio del file come di consueto.

I file con estensione *.csx* di un'app per le funzioni vengono compilati quando viene inizializzata un'istanza. Questo passaggio di compilazione implica che operazioni quali l'avvio a freddo potrebbero richiedere più tempo per le funzioni script C# rispetto alle librerie di classi C#. Questo passaggio di compilazione è anche il motivo per cui le funzioni script C# sono modificabili nel portale di Azure, mentre le librerie di classi C# non lo sono.

## <a name="binding-to-arguments"></a>Associazione agli argomenti

I dati di input o output sono associati a un parametro di una funzione script C# tramite la proprietà `name` nel file di configurazione *function.json*. L'esempio seguente mostra un file *function.json* e un file *run.csx* per una funzione attivata dalla coda. Il parametro che riceve i dati dal messaggio della coda è denominato `myQueueItem` perché questo è il valore della proprietà `name`.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

L'istruzione `#r` è spiegata [più avanti in questo articolo](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Tipi supportati per le associazioni

Ogni associazione supporta determinati tipi. Ad esempio è possibile usare un trigger di BLOB con un parametro stringa, un parametro POCO, un parametro `CloudBlockBlob` o uno dei molti altri tipi supportati. L'[articolo di riferimento sull'associazione relativo alle associazioni BLOB](functions-bindings-storage-blob.md#trigger---usage) elenca tutti i tipi di parametri supportati per i trigger di BLOB. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md) e i [documenti di riferimento sull'associazione per ogni tipo di associazione](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Riferimento a classi personalizzate

Se è necessario usare una classe Plain Old CLR Object (POCO) personalizzata, è possibile includere la definizione della classe all'interno dello stesso file o inserirla in un file separato.

Di seguito è riportato un esempio *run.csx* che include una definizione di classe POCO.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Una classe POCO deve avere un metodo Get e un metodo Set definiti per ogni proprietà.

## <a name="reusing-csx-code"></a>Riutilizzo del codice CSX

È possibile usare classi e metodi definiti in altri file con estensione *.csx* nel file *run.csx*. A questo scopo, usare direttive `#load` nel file *run.csx*. Nell'esempio seguente, una routine di registrazione denominata `MyLogger` viene condivisa in *myLogger.csx* e caricata in *run.csx* usando la direttiva `#load`:

Esempio di *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Esempio di *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

L'uso di un file con estensione *.csx* condiviso è una prassi comune quando si vuole tipizzare fortemente i dati passati tra le funzioni usando un oggetto POCO. Nell'esempio semplificato seguente, un trigger HTTP e un trigger della coda condividono un oggetto POCO denominato `Order` per tipizzare fortemente i dati dell'ordine:

File *run.csx* di esempio per il trigger HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

File *run.csx* di esempio per il trigger della coda:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

File *order.csx* di esempio:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

È possibile usare un percorso relativo con la direttiva `#load` :

* `#load "mylogger.csx"` carica un file che si trova nella cartella della funzione.
* `#load "loadedfiles\mylogger.csx"` carica un file che si trova in una sottocartella della cartella della funzione.
* `#load "..\shared\mylogger.csx"` carica un file che si trova in una cartella allo stesso livello della cartella della funzione, ovvero direttamente in *wwwroot*.

La direttiva `#load` è compatibile solo con i file con estensione *.csx* e non lo è con i file con estensione *.cs*.

## <a name="binding-to-method-return-value"></a>Associazione al valore restituito dal metodo

È possibile usare un valore restituito del metodo per un'associazione di output, usando il nome `$return` in *function.json*:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "$return",
    "queueName": "outqueue",
    "connection": "MyStorageConnectionString",
}
```

```csharp
public static string Run(string input, TraceWriter log)
{
    return input;
}
```

## <a name="writing-multiple-output-values"></a>Scrittura di più valori di output

Per scrivere più valori in un'associazione di output, usare i tipi [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Questi tipi sono raccolte di sola scrittura che vengono scritte nell'associazione di output durante il completamento del metodo.

Questo esempio scrive più messaggi in coda nella stessa coda usando `ICollector`:

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>Registrazione

Per registrare l'output nei log in streaming in C#, includere un argomento di tipo `TraceWriter`. È consigliabile denominarlo `log`. Evitare di usare `Console.Write` in Funzioni di Azure. 

`TraceWriter` è definito in [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Il livello di registrazione per `TraceWriter` può essere configurato in [host.json](functions-host-json.md).

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Per informazioni su un framework di registrazione più recente che è possibile usare invece di `TraceWriter`, vedere [Scrivere i log nelle funzioni C#](functions-monitoring.md#write-logs-in-c-functions) nell'articolo **Monitorare Funzioni di Azure**.

## <a name="async"></a>Async

Per rendere una funzione asincrona, usare la parola chiave `async` e restituire un oggetto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="cancellation-tokens"></a>Token di annullamento

Alcune operazioni richiedono l'arresto normale. Mentre è sempre preferibile scrivere il codice per la gestione degli arresti anomali, quando si vuole gestire le richieste di arresto definire un argomento tipizzato [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  È fornito un `CancellationToken` per segnalare che viene avviato un arresto dell'host.

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    string blobName,
    Stream blobInput,
    Stream blobOutput,
    CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importazione di spazi dei nomi

Se è necessario importare spazi dei nomi, è possibile farlo come al solito con la clausola `using` .

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Gli spazi dei nomi seguenti vengono importati automaticamente e sono quindi facoltativi:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Riferimento ad assembly esterni

Per gli assembly di framework aggiungere riferimenti usando la direttiva `#r "AssemblyName"` .

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Gli assembly seguenti vengono aggiunti automaticamente dall'ambiente di hosting di Funzioni di Azure:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

È possibile fare riferimento agli assembly seguenti con il nome semplice (ad esempio, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Fare riferimento ad assembly personalizzati

Per fare riferimento a un assembly personalizzato, è possibile usare un assembly *condiviso* o un assembly *privato*:
- Gli assembly condivisi sono condivisi da tutte le funzioni all'interno di un'app di funzione. Per fare riferimento a un assembly personalizzato, caricare l'assembly nell'app di funzione, ad esempio una cartella `bin` nella radice dell'app per le funzioni. 
- Gli assembly privati fanno parte del contesto di una funzione specificata e supportano il caricamento laterale di versioni diverse. Gli assembly privati devono essere caricati in una cartella `bin` nella directory di funzione. Fare riferimento agli assembly usando il nome del file, ad esempio `#r "MyAssembly.dll"`. 

Per informazioni su come caricare i file nella cartella della funzione, vedere la sezione sulla [gestione dei pacchetti](#using-nuget-packages).

### <a name="watched-directories"></a>Directory controllate

La directory che contiene il file di script della funzione viene controllata automaticamente per le modifiche agli assembly. Per controllare le modifiche agli assembly in altre directory, aggiungerle all'elenco `watchDirectories` in [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Uso dei pacchetti NuGet

Per usare i pacchetti NuGet in una funzione C#, caricare un file *project.json* nella cartella della funzione nel file system dell'app per le funzioni. Di seguito è riportato un esempio di file *project.json* che aggiunge un riferimento a Microsoft.ProjectOxford.Face versione 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

In Funzioni di Azure 1.x è supportato solo .NET Framework 4.6, perciò verificare che nel file *project.json* sia specificato `net46` come illustrato qui.

Quando si carica un file *project.json* , il runtime ottiene i pacchetti e aggiunge automaticamente riferimenti agli assembly dei pacchetti. Non è necessario aggiungere direttive `#r "AssemblyName"` . Per usare i tipi definiti nei pacchetti NuGet è sufficiente aggiungere le istruzioni `using` necessarie al file *run.csx*. 

Nel runtime di Funzioni NuGet ripristina le operazioni confrontando `project.json` e `project.lock.json`. Se gli indicatori di data e ora dei file **non** corrispondono, NuGet esegue un ripristino e aggiorna i pacchetti. In caso contrario, NuGet **non** esegue alcun ripristino. Pertanto, `project.lock.json` non deve essere distribuito, in quanto induce NuGet a saltare il ripristino del pacchetto. Per evitare la distribuzione del file di blocco, aggiungere `project.lock.json` al `.gitignore` file.

Per usare un feed NuGet personalizzato, specificare il feed in un *Nuget.Config* nella radice dell'app per le funzioni. Per altre informazioni, vedere [Configuring NuGet behavior](/nuget/consume-packages/configuring-nuget-behavior) (Configurazione del comportamento di NuGet).

### <a name="using-a-projectjson-file"></a>Uso di un file project.json

1. Aprire la funzione nel portale di Azure. La scheda dei log mostra l'output di installazione del pacchetto.
2. Per caricare un file project.json, usare uno dei metodi descritti nella sezione [Come aggiornare i file delle app per le funzioni](functions-reference.md#fileupdate) dell'argomento Guida di riferimento per gli sviluppatori di Funzioni di Azure.
3. Dopo il caricamento del file *project.json* , l'output visualizzato nel log in streaming della funzione è simile all'esempio seguente:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variabili di ambiente

Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `System.Environment.GetEnvironmentVariable`come illustrato nell'esempio di codice seguente:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
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
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Associazione in fase di esecuzione

In C# e altri linguaggi .NET, è possibile usare un metodo di associazione [imperativa](https://en.wikipedia.org/wiki/Imperative_programming) anziché [ *dichiarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) in *function.json*. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Con questo modello è possibile associare rapidamente i dati ad associazioni di input e output supportate nel codice della funzione.

Definire un'associazione imperativa, come segue:

- **Non** includere una voce in *function.json* per le associazioni imperative da eseguire.
- Passare un parametro di input [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Usare il seguente modello C# per eseguire l'associazione dati.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` è l'attributo .NET che definisce l'associazione e `T` è un tipo di input o output supportato da quel tipo di associazione. `T` non può essere un tipo di parametro `out`, ad esempio `out JObject`. L'associazione di output della tabella App per dispositivi mobili, ad esempio, supporta[ sei tipi di output](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ma è possibile usare solo [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) per `T`.

### <a name="single-attribute-example"></a>Esempio con un solo attributo

L'esempio di codice seguente crea un [associazione di output del BLOB di archiviazione](functions-bindings-storage-blob.md#output) con percorso del BLOB definito in fase di esecuzione, quindi scrive una stringa per il BLOB.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definisce l'associazione di input o output del [BLOB di archiviazione](functions-bindings-storage-blob.md) e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) è un tipo di associazione di output supportato.

### <a name="multiple-attribute-example"></a>Esempio con più attributi

L'esempio precedente ottiene l'impostazione dell'app per la stringa di connessione dell'account di archiviazione principale dell'app, ovvero `AzureWebJobsStorage`. È possibile specificare un'impostazione app personalizzata da usare per l'account di archiviazione aggiungendo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando la matrice di attributi in `BindAsync<T>()`. Usare un parametro `Binder` e non `IBinder`.  Ad esempio: 

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

Nella tabella seguente vengono elencati gli attributi .NET per ogni tipo di associazione e i pacchetti in cui sono definiti.

> [!div class="mx-codeBreakAll"]
| Associazione | Attributo | Aggiungi riferimento |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Hub eventi | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| App per dispositivi mobili | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Hub di notifica | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Bus di servizio | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Coda di archiviazione | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| BLOB di archiviazione | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabella di archiviazione | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui trigger e le associazioni](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Altre informazioni sulle procedure consigliate per Funzioni di Azure](functions-best-practices.md)
