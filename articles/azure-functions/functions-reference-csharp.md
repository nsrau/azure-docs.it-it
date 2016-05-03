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
	ms.date="04/14/2016"
	ms.author="chrande"/>

# Guida di riferimento per gli sviluppatori C# di Funzioni di Azure

L'esperienza C# per Funzioni di Azure si basa su Azure WebJobs SDK. I dati vengono trasmessi alla funzione C# tramite argomenti del metodo. I nomi di argomento vengono specificati in `function.json` e sono disponibili nomi predefiniti per l'accesso a elementi quali il logger di funzioni e i token di annullamento.

Questo articolo presuppone che l'utente abbia già letto la [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)

## Funzionamento di CSX

Il formato `.csx` consente di scrivere meno "boilerplate" e concentrarsi solo sulla scrittura una funzione C#. Per Funzioni di Azure è sufficiente includere tutti i riferimenti agli assembly e gli spazi dei nomi necessari, come al solito, e invece di racchiudere tutto in uno spazio dei nomi e una classe, è possibile definire solo il metodo `Run`. Se è necessario includere classi, ad esempio per definire gli oggetti POCO, si può includere una classe nello stesso file.

## Associazione agli argomenti

I diversi binding sono associati a una funzione C# tramite la proprietà `name` nella configurazione di *function.json*. Per ogni binding sono disponibili tipi supportati specifici documentati singolarmente. Ad esempio, un trigger di BLOB può supportare una stringa, un oggetto POCO o diversi altri tipi. È possibile usare il tipo più adatto alle proprie esigenze.

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

Per registrare l'output nei log in streaming in C#, è possibile includere un argomento tipizzato `TraceWriter`. È consigliabile denominarlo `log`. È consigliabile evitare `Console.Write` in Funzioni di Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
}
```

## Async

Per rendere una funzione asincrona, usare la parola chiave `async` e restituire un oggetto `Task` .

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

In alcuni casi possono essere presenti operazioni sensibili all'arresto. Anche se è sempre preferibile scrivere codice che possa gestire un arresto anomalo, nei casi in cui si vogliono gestire richieste di arresto normale, definire un argomento tipizzato [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). Se viene attivato l'arresto di un host, verrà fornito un argomento `CancellationToken`.

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
* `System.Linq`
* `System.Net.Http`
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

Gli assembly seguenti sono inoltre casi speciali ed è possibile farvi riferimento tramite simplename, ad esempio `#r "AssemblyName"`:

* `Newtonsoft.Json`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se è necessario fare riferimento a un assembly privato, è possibile caricare il file di assembly in una cartella `bin` relativa alla funzione e farvi riferimento usando il nome file, ad esempio `#r "MyAssembly.dll"`.

## Gestione dei pacchetti

Per usare i pacchetti NuGet in una funzione C#, caricare un file *project.json* nella cartella della funzione nel file system dell'app di funzione. Ecco un esempio di file*project.json* che aggiunge un riferimento a Microsoft.ProjectOxford.Face versione 1.1.0::

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

Quando si carica un file *project.json*, il runtime ottiene i pacchetti e aggiunge automaticamente riferimenti agli assembly dei pacchetti. Non è necessario aggiungere direttive `#r "AssemblyName"`. Per usare i tipi definiti nei pacchetti NuGet, è sufficiente aggiungere le istruzioni `using` necessarie al file *run.csx*.

### Come caricare un file project.json

Assicurarsi prima di tutto che l'app di funzione sia in esecuzione aprendo la funzione nel portale di Azure. In questo modo è anche possibile accedere ai log in streaming in cui verrà visualizzato l'output di installazione dei pacchetti.

Le app di funzione sono basate sul servizio app, quindi tutte le[opzioni di distribuzione disponibili per le app Web standard](../app-service-web/web-sites-deploy.md) sono disponibili anche per le app di funzione. Ecco alcuni metodi che è possibile usare.

#### Per caricare il file project.json usando Visual Studio Online (Monaco)

1. Nel portale di Funzioni di Azure fare clic su **Impostazioni dell'app di funzione**.

2. In **Impostazioni avanzate** fare clic su **Vai alle impostazioni del servizio app**.

3. Fare clic su **Strumenti**.

4. In **Sviluppo** fare clic su **Visual Studio Online**.

5. Fare clic su **Sì** se l'opzione non è già abilitata, quindi su **Vai**.

6. Una volta completato il caricamento di Visual Studio Online, selezionare il file *project.json* e trascinarlo nella cartella con il nome della funzione.

#### Per caricare il file project.json usando l'endpoint SCM (Kudu) dell'app di funzione

1. Passare a *https://<function_app_name>.scm.azurewebsites.net*.

2. Fare clic su **Debug Console > CMD**.

3. Passare a *D:\\home\\site\\wwwroot<nome\_funzione>*.

4. Selezionare il file *project.json* e trascinarlo nella cartella (nella griglia di file).

#### Per caricare il file project.json usando FTP

1. Per la configurazione di FTP, seguire le istruzioni illustrate [qui](../app-service-web/web-sites-deploy.md#ftp).

2. Quando si è connessi al sito dell'app di funzione, copiare il file *project.json* in */sito/wwwroot/<function_name>*.

#### Log di installazione del pacchetto 

Dopo aver caricato il file *project.json*, nel log in streaming della funzione verrà visualizzato l'output simile all'esempio seguente:

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

## Riutilizzo del codice CSX

È possibile usare classi e metodi definiti in altri file con estensione *csx* nel file *run.csx*. A questo scopo, usare le direttive `#load` nel file *run.csx*, come illustrato nell'esempio seguente.

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

* `#load "loadedfiles\mylogger.csx"` carica un file che si trova in una cartella nella cartella della funzione.

* `#load "..\shared\mylogger.csx"` carica un file si trova in una cartella allo stesso livello della cartella della funzione, ovvero direttamente in *wwwroot*.
 
La direttiva `#load` funziona solo con i file con estensione *csx* (script C# ), ma non con i file con estensione *cs*.

## Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0420_2016-->