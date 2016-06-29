<properties
	pageTitle="Guida di riferimento per gli sviluppatori di Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come sviluppare Funzioni di Azure in C#."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Guida di riferimento per gli sviluppatori C# di Funzioni di Azure

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Node.JS](../articles/azure-functions/functions-reference-node.md)
 
L'esperienza C# per Funzioni di Azure si basa su Azure WebJobs SDK. I dati vengono trasmessi alla funzione C# tramite argomenti del metodo. I nomi di argomento sono specificati in `function.json` e sono disponibili nomi predefiniti per l'accesso a elementi quali il logger delle funzioni e i token di annullamento.

Questo articolo presuppone che l'utente abbia già letto [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md).

## Funzionamento di CSX

Il formato `.csx` consente di scrivere meno "boilerplate" e concentrarsi solo sulla scrittura di una funzione C#. Per Funzioni di Azure è sufficiente includere tutti i riferimenti agli assembly e gli spazi dei nomi necessari, come al solito, e invece di racchiudere tutto in uno spazio dei nomi e una classe, è possibile definire solo il metodo `Run`. Se è necessario includere classi, ad esempio per definire gli oggetti POCO, si può includere una classe nello stesso file.

## Associazione agli argomenti

I vari binding sono associati a una funzione C# tramite la proprietà `name` nella configurazione di *function.json*. Per ogni binding sono disponibili tipi supportati specifici documentati singolarmente. Ad esempio, un trigger di BLOB può supportare una stringa, un oggetto POCO o diversi altri tipi. È possibile usare il tipo più adatto alle proprie esigenze.

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

## Registrazione

Per registrare l'output nei log in streaming in C#, è possibile includere un argomento tipizzato `TraceWriter`. È consigliabile denominarlo `log`. Si consiglia di evitare `Console.Write` in Funzioni di Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## Async

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

## Token di annullamento

In alcuni casi possono essere presenti operazioni sensibili all'arresto. Mentre è sempre preferibile scrivere il codice per la gestione degli arresti anomali, per gestire le richieste di arresto normale si definisce un argomento tipizzato [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). Se viene attivato l'arresto di un host, verrà fornito un argomento `CancellationToken`.

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

## Importazione di spazi dei nomi

Se è necessario importare spazi dei nomi, è possibile farlo come al solito con la clausola `using`.

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
* `Microsoft.Azure.WebJobs.Host`.

## Riferimento ad assembly esterni

Per gli assembly di framework aggiungere riferimenti usando la direttiva `#r "AssemblyName"`.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Gli assembly seguenti vengono aggiunti automaticamente dall'ambiente di hosting di Funzioni di Azure:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Gli assembly seguenti sono anche casi speciali ai quali è possibile fare riferimento tramite simplename, ad esempio `#r "AssemblyName"`:

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Per fare riferimento a un assembly privato è possibile caricare il file dell'assembly in una cartella `bin` relativa alla funzione e farvi riferimento usando il nome file, ad esempio `#r "MyAssembly.dll"`. Per informazioni su come caricare i file nella cartella della funzione vedere la sezione seguente sulla gestione dei pacchetti.

## Gestione dei pacchetti

Per usare i pacchetti NuGet in una funzione C#, caricare un file *project.json* nella cartella della funzione nel file system dell'app per le funzioni. Ecco un esempio di file *project.json* che aggiunge un riferimento a Microsoft.ProjectOxford.Face versione 1.1.0:

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

Poiché è supportato solo .NET Framework 4.6, verificare che nel file *project.json* sia specificato `net46` come qui illustrato.

Quando si carica un file *project.json*, il runtime ottiene i pacchetti e aggiunge automaticamente riferimenti agli assembly dei pacchetti. Non è necessario aggiungere direttive `#r "AssemblyName"`. Per usare i tipi definiti nei pacchetti NuGet è sufficiente aggiungere le istruzioni `using` necessarie al file *run.csx*.


### Come caricare un file project.json

1. Assicurarsi prima di tutto che l'app di funzione sia in esecuzione aprendo la funzione nel portale di Azure. 

	In questo modo è anche possibile accedere ai log in streaming in cui verrà visualizzato l'output di installazione dei pacchetti.

2. Per caricare un file project.json, usare uno dei metodi descritti nella sezione **Come aggiornare i file delle app per le funzioni** dell'argomento [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md#fileupdate).

3. Dopo il caricamento del file *project.json*, l'output visualizzato nel log in streaming della funzione è simile all'esempio seguente:

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

## Variabili di ambiente

Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `System.Environment.GetEnvironmentVariable` come illustrato nell'esempio di codice seguente:

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

## Riutilizzo del codice CSX

È possibile usare classi e metodi definiti in altri file con estensione *csx* nel file *run.csx*. A tale scopo, usare direttive `#load` nel file *run.csx*, come illustrato nell'esempio seguente.

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

È possibile usare un percorso relativo con la direttiva `#load`:

* `#load "mylogger.csx"` carica un file che si trova nella cartella della funzione.

* `#load "loadedfiles\mylogger.csx"` carica un file che si trova in una sottocartella della cartella della funzione.

* `#load "..\shared\mylogger.csx"` carica un file che si trova in una cartella allo stesso livello della cartella della funzione, ovvero direttamente in *wwwroot*.
 
La direttiva `#load` funziona solo con i file con estensione *csx* (script C# ), non con i file con estensione *cs*.

## Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0615_2016-->