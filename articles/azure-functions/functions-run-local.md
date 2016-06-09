<properties
	pageTitle="Sviluppare ed eseguire localmente le funzioni di Azure | Microsoft Azure"
	description="Informazioni su come scrivere codice per le funzioni di Azure e testarle in Visual Studio prima di eseguirle nel Servizio app di Azure."
	services="functions"
	documentationCenter="na"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="functions"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="tdykstra"/>

# Come scrivere codice per le funzioni di Azure e testarle in Visual Studio

## Panoramica

Questo articolo illustra come eseguire [Funzioni di Azure](functions-overview.md) localmente scaricando il repository [WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/) di GitHub ed eseguendo la soluzione di Visual Studio in esso contenuta.

Il runtime per Funzioni di Azure è un'implementazione del progetto WebJobs.Script open source. Questo progetto è basato a sua volta su [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) ed entrambi i framework possono essere eseguiti localmente. È tuttavia necessario connettersi a un account di archiviazione di Azure, perché WebJobs SDK usa funzionalità dell'account di archiviazione non supportate dall'emulatore di archiviazione.

È possibile scrivere codice per le funzioni e testarle con facilità nel portale di Azure, ma a volte può risultare utile lavorare localmente su di esse prima dell'esecuzione in Azure. Ad esempio, la scrittura di codice per alcune lingue supportate da Funzioni di Azure risulta più semplice in Visual Studio perché è disponibile [IntelliSense](https://msdn.microsoft.com/library/hcw1s69b.aspx). Anche se è possibile eseguire il debug di una funzione in modalità remota, il debug locale potrebbe risultare più veloce e più semplice. L'esecuzione locale consente di eseguire il debug e inserire punti di interruzione nel codice delle funzioni, oltre che nel codice host dello script dei processi Web.

>[AZURE.NOTE] Funzioni di Azure è attualmente disponibile in anteprima e l'esperienza complessiva, inclusi gli strumenti, è ancora in fase di rapido sviluppo. Le procedure illustrate in questo articolo non riflettono l'esperienza di sviluppo locale finale. È possibile [inviare commenti e suggerimenti](https://feedback.azure.com/forums/355860-azure-functions).

## Prerequisiti

### Account Azure con un'app di funzione

In questo articolo si presuppone una familiarità con [Funzioni di Azure](functions-overview.md) nel portale e con i concetti di Funzioni di Azure, ad esempio [trigger, associazioni e JobHost](functions-reference.md).

Quando le funzioni vengono eseguite in locale, l'output viene visualizzato nella finestra della console, ma è necessario usare anche il dashboard ospitato da un'app di funzione live per visualizzare le chiamate di funzione e i log.

### Visual Studio 2015 con Azure SDK per .NET più recente

Se Visual Studio 2015 o Azure SDK corrente non sono disponibili, [scaricare Azure SDK per Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio 2015 viene automaticamente installato con l'SDK, se necessario.

### Prerequisiti condizionali

Alcune risorse di Azure e alcune installazioni di software sono necessarie solo se si prevede di eseguire funzioni che le usano, ad esempio:

* Risorse di Azure
	* Bus di servizio
	* Tabelle semplici
	* DocumentDB
	* Hub eventi
	* Hub di notifica

* Compilatori e motori di script
	* F#
	* BASH
	* Python
	* PHP

Per informazioni dettagliate su questi requisiti, incluse le variabili di ambiente che è necessario configurare, vedere le [pagine wiki per il repository WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/wiki/home)

Se si vuole contribuire al progetto WebJobs.SDK, saranno necessari tutti i prerequisiti condizionali per eseguire test completi.

## Esecuzione locale

1. [Clonare](https://github.com/Azure/azure-webjobs-sdk-script/) o [scaricare](https://github.com/Azure/azure-webjobs-sdk-script/archive/master.zip) il repository Webjobs.Script.

2. Impostare le variabili di ambiente per le stringhe di connessione di archiviazione.

	* AzureWebJobsStorage
	* AzureWebJobsDashboard

	È possibile ottenere questi valori dal pannello del portale **Impostazioni applicazione** del servizio app per un'app di funzione.

	a. Nel pannello **App di funzione** fare clic su **Impostazioni dell'app di funzione**.

	![Fare clic su Impostazioni dell'app di funzione](./media/functions-run-local/clickfuncappsettings.png)
 
	b. Nel pannello **Impostazioni dell'app di funzione** fare clic su **Vai alle impostazioni del servizio app**.

	![Fare clic sulle impostazioni del servizio app](./media/functions-run-local/clickappsvcsettings.png)
 
	c. Nel pannello **Impostazioni** fare clic su **Impostazioni applicazione**.

	![Fare clic su Impostazioni applicazione](./media/functions-run-local/clickappsettings.png)
 
	d. Nel pannello **Impostazioni applicazione** scorrere verso il basso fino alla sezione **Impostazioni app** e trovare le impostazioni di WebJobs SDK.

	![Impostazioni dei processi Web](./media/functions-run-local/wjsettings.png)

	e. Impostare una variabile di ambiente con lo stesso nome e lo stesso valore dell'impostazione dell'app `AzureWebJobsStorage`.

	f. Eseguire la stessa operazione per l'impostazione dell'app `AzureWebJobsDashboard`.

2. Creare una variabile di ambiente denominata AzureWebJobsServiceBus e impostarla sulla stringa di connessione del bus di servizio.

	Questa variabile di ambiente è necessaria per le associazioni del bus di servizio ed è consigliabile configurarla anche se non si usano le associazioni del bus di servizio. In alcuni scenari è possibile che vengano visualizzate eccezioni se la stringa di connessione del bus di servizio non è configurata, indipendentemente dalle associazioni in uso.

3. Assicurarsi che siano state configurate eventuali altre variabili di ambiente necessarie. Vedere la sezione precedente [Prerequisiti condizionali](#conditional-prerequisites).

4. Avviare Visual Studio e quindi aprire la soluzione WebJobs.Script.

6. Configurare il progetto di avvio. Per eseguire funzioni che usano HTTP o trigger WebHook, scegliere **WebJobs.Script.WebHost**. In caso contrario, scegliere **WebJobs.Script.Host**.

4. Se il progetto di avvio è WebJobs.Script.Host:

	a. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto WebJobs.Script.Host, quindi scegliere **Proprietà**.

	b. Nella scheda **Debug** della finestra **Proprietà progetto** impostare **Argomenti della riga di comando** su `..\..\..\..\sample`.

	![Argomenti della riga di comando](./media/functions-run-local/cmdlineargs.png)

	Questo è un percorso relativo per la cartella *sample* nel repository. La cartella *sample* contiene un file *host.json* che include impostazioni globali e una cartella per ogni funzione di esempio.

	Per iniziare, l'approccio più semplice consiste nell'usare la cartella *sample* disponibile. Sarà possibile aggiungere in seguito funzioni personalizzate alla cartella *sample* oppure usare qualsiasi cartella contenente un file *host.json* e cartelle di funzione.

5. Se il progetto di avvio è WebJobs.Script.WebHost:

	a. Configurare una variabile di ambiente AzureWebJobsScriptRoot specificando il percorso completo della cartella `sample`.

	b. Riavviare Visual Studio per recuperare il nuovo valore della variabile di ambiente.

	Per altre informazioni su come eseguire le funzioni trigger HTTP, vedere la sezione [Chiavi API](#api-keys).

5. Aprire il file *sample\\host.json* e aggiungere una proprietà `functions` per specificare le funzioni da eseguire.

	Ad esempio, il codice JSON seguente farà in modo che WebJobs SDK JobHost cerchi solo due funzioni.

		{
		  "functions": [ "TimerTrigger-CSharp", "QueueTrigger-CSharp"],
		  "id": "5a709861cab44e68bfed5d2c2fe7fc0c"
		}

	Quando si usa una cartella personalizzata invece della cartella *sample*, includere nella cartella solo le funzioni da eseguire. Sarà quindi possibile omettere la proprietà `functions` in *host.json*.
 
6. Compilare ed eseguire la soluzione.

	La finestra della console mostra che JobHost trova solo le funzioni specificate nel file `host.json`.

		Found the following functions:
		Host.Functions.QueueTrigger-CSharp
		Host.Functions.TimerTrigger-CSharp
		Job host started

	All'inizio del progetto di WebHost viene visualizzata una pagina del browser vuota in quanto manca il contenuto per l'URL di base del progetto. Per informazioni sugli URL da usare per le funzioni di trigger HTTP, vedere la sezione [Chiavi API per trigger HTTP](#apikeys).

## Visualizzazione dell'output della funzione

Passare al dashboard per l'app di funzione per visualizzare le chiamate di funzione e il rispettivo output di log.

Il dashboard è disponibile all'URL seguente:

	https://{function app name}.scm.azurewebsites.net/azurejobs/#/functions

La pagina **Functions** (Funzioni) visualizza un elenco di funzioni eseguite e un elenco di chiamate di funzione.

![Dettagli della chiamata](./media/functions-run-local/invocationdetail.png)

Fare clic su una chiamata per visualizzare la pagina **Invocation Details** (Dettagli della chiamata), che indica quando è stata attivata la funzione, il tempo di esecuzione approssimativo e il completamento corretto. Fare clic sul pulsante **Toggle Output** (Attiva/Disattiva output) per visualizzare i log scritti dal codice della funzione.

![Dettagli della chiamata](./media/functions-run-local/invocationdetail.png)

## <a id="apikeys"></a> Chiavi API per trigger HTTP

Per eseguire una funzione HTTP o WebHook, è necessaria una chiave API, a meno che non si includa `"authLevel": "anonymous"` nel file *function.json*.

Ad esempio, se la chiave API è `12345`, è possibile attivare la funzione *HttpTrigger* con l'URL seguente quando il progetto WebJobs.Script.WebHost è in esecuzione.

	http://localhost:28549/api/httptrigger?code=12345

In alternativa, è possibile inserire la chiave API nell'intestazione HTTP `x-functions-key`.

Le chiavi API vengono archiviate nei file `.json` nella cartella [App\_Data/secrets](https://github.com/Azure/azure-webjobs-sdk-script/tree/master/src/WebJobs.Script.WebHost/App_Data/secrets) del progetto WebJobs.Script.WebHost.

### Chiavi API applicabili a tutte le funzioni HTTP e WebHook

Il file *host.json* nella cartella *App\_Data/secrets* ha due chiavi:

```json
{
  "masterKey": "hyexydhln844f2mb7hyexydhln844f2mb7",
  "functionKey": "7hyexydhn844f2mb7hyexydhln844f2mb7"
}
```

La proprietà `functionKey` archivia una chiave che può essere usata per qualsiasi funzione HTTP o WebHook, se non viene definito alcun override per la funzione specifica. Questa funzionalità elimina la necessità di definire sempre le chiavi API per ogni funzione creata.

La proprietà `masterKey` archivia una chiave che risulta utile in alcuni scenari di test:

* Se si chiama una funzione WebHook con una chiave master, WebJobs SDK ignora la convalida della firma del provider di WebHook.

* Se si chiama una funzione HTTP o WebHook con una chiave master, la funzione viene attivata anche se è disabilitata nel file *function.json*. Questo approccio viene usato nel portale di Azure per consentire il funzionamento del pulsante **Esegui** anche per le funzioni disabilitate.
 
### Chiavi API applicabili alle singole funzioni

I file denominati *{nome funzione}.json* contengono la chiave API per una funzione specifica. Ad esempio, il contenuto JSON seguente in *App\_Data/secrets/HttpTrigger.json* imposta la chiave API per la funzione `HttpTrigger`.

```json
{
  "key": "844f2mdhn844f2mb7hyexydhln844f2mb7"
}
```

## Uso dei riferimenti al pacchetto NuGet nelle funzioni  

Data la modalità dell'elaborazione attuale dei riferimenti NuGet, è importante intervenire sul file *project.json* quando l'host è in esecuzione. L'host monitora infatti le modifiche al file e avvia un'operazione di ripristino quando ne rileva. Il percorso deve anche contenere *NuGet.exe* (versione consigliata 3.3.0) oppure è necessario aver configurato una variabile di ambiente denominata AzureWebJobs\_NuGetPath con il percorso *NuGet.exe*.

## Risoluzione dei problemi

Le modifiche alle variabili di ambiente apportate quando Visual Studio è in esecuzione non vengono rilevate automaticamente. Se è stata aggiunta o modificata una variabile di ambiente dopo l'avvio di Visual Studio, chiudere Visual Studio e riavviarlo per assicurarsi che vengano rilevati i valori correnti.

Quando si esegue il debug, è possibile ottenere altre informazioni sulle eccezioni selezionando l'opzione relativa alle **eccezioni Common Language Runtime** nella finestra **Impostazioni eccezione**. Per aprire la finestra, premere CTRL-ALT-E.

Per ottenere altre informazioni sulle eccezioni durante il debug, è anche possibile impostare un punto di interruzione nel blocco `catch` del ciclo principale per l'host di script, disponibile nel progetto WebJobs.Script, in *Host/ScriptHostManager.cs* nel metodo `RunAndBlock`.

## Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0525_2016-->