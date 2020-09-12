---
title: Usare le API e gli SDK di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Vedere come usare le API dei dispositivi gemelli digitali di Azure, incluso via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f61773e693ad1276ef48b2f26ef6e3e8f59e2992
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612369"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Usare le API e gli SDK di Gemelli digitali di Azure

I dispositivi gemelli digitali di Azure sono dotati di API del **piano di controllo** e **API del piano dati** per gestire l'istanza e i relativi elementi. Questo articolo fornisce una panoramica delle API disponibili e i metodi per interagire con essi. È possibile usare le API REST direttamente con le loro spavalderia associate oppure tramite un SDK.

## <a name="overview-control-plane-apis"></a>Panoramica: API del piano di controllo

Le API del piano di controllo vengono usate per gestire l'istanza di Azure Digital Twins nel suo complesso, in modo da coprire operazioni come la creazione o l'eliminazione dell'intera istanza. Questi vengono usati anche per creare ed eliminare gli endpoint.

La versione più recente dell'API del piano di controllo per l'anteprima pubblica è _**2020-03-01-Preview**_.

Per usare le API del piano di controllo:
* È possibile chiamare direttamente le API facendo riferimento alla [cartella spavalderia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins)più recente. Questo repository include anche una cartella di esempi che illustrano l'utilizzo.
* È attualmente possibile accedere ad SDK per le API di controllo in...
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([origine](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([riferimento [generato automaticamente]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview&preserve-view=true))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([origine](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([riferimento [generato automaticamente]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview&preserve-view=true))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([origine](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([origine](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go-source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

È anche possibile esercitare le API del piano di controllo interagendo con i dispositivi gemelli digitali di Azure tramite il [portale di Azure](https://portal.azure.com) e l' [interfaccia](how-to-use-cli.md)della riga di comando.

## <a name="overview-data-plane-apis"></a>Panoramica: API del piano dati

Le API del piano dati vengono usate per gestire gli elementi all'interno dell'istanza di Azure Digital gemelli. Sono incluse operazioni come la creazione di route, il caricamento di modelli, la creazione di relazioni e la gestione dei dispositivi gemelli. Possono essere suddivise nelle categorie seguenti:
* **DigitalTwinsModels** : la categoria DigitalTwinsModels contiene le API per gestire i [modelli](concepts-models.md) in un'istanza di dispositivi gemelli digitali di Azure. Le attività di gestione includono il caricamento, la convalida, il recupero e l'eliminazione di modelli creati in DTDL.
* **DigitalTwins** : la categoria DigitalTwins contiene le API che consentono agli sviluppatori di creare, modificare ed eliminare i dispositivi [gemelli digitali](concepts-twins-graph.md) e le relative relazioni in un'istanza di dispositivi gemelli digitali di Azure.
* **Query** : la categoria query consente agli sviluppatori [di individuare set di gemelli digitali nel grafico gemello tra le](how-to-query-graph.md) relazioni.
* **EventRoutes** : la categoria EventRoutes contiene le API per [indirizzare i dati](concepts-route-events.md), attraverso il sistema e i servizi downstream.

La versione più recente dell'API del piano dati per l'anteprima pubblica è _**2020-05-31-Preview**_. La versione dell'API _2020-03-01-Preview_ per le operazioni del piano dati è ora deprecata.

Per usare le API del piano dati:
* È possibile chiamare direttamente le API, da...
   - riferimento alla [cartella spavalderia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)più recente. Questo repository include anche una cartella di esempi che illustrano l'utilizzo. 
   - visualizzazione della [documentazione di riferimento](https://docs.microsoft.com/rest/api/azure-digitaltwins/)per le API.
* È possibile usare **.NET (C#)** SDK. Per utilizzare .NET SDK...
   - è possibile visualizzare e aggiungere il pacchetto da NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - è possibile trovare l'origine SDK, inclusa una cartella di esempi, in GitHub: [libreria client di dispositivi digitali gemelli di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - è possibile visualizzare la [documentazione di riferimento dell'SDK](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview&preserve-view=true).
   - per visualizzare informazioni dettagliate ed esempi di utilizzo, continuare con la sezione [.NET (C#) SDK (piano dati)](#net-c-sdk-data-plane) di questo articolo.
* È possibile usare **JavaScript** SDK. Per utilizzare JavaScript SDK...
   - è possibile visualizzare e installare il pacchetto da NPM: [libreria client di Azure Digital gemelli di Azure per JavaScript](https://www.npmjs.com/package/@azure/digital-twins/v/1.0.0-preview.1)
* È possibile generare un SDK per un altro linguaggio con autorest. Seguire le istruzioni in [*procedura: creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest*](how-to-create-custom-sdks.md).

È anche possibile esercitare le API del piano dati interagendo con i dispositivi gemelli digitali di Azure tramite l' [interfaccia](how-to-use-cli.md)della riga di comando.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (piano dati)

Azure Digital Twins .NET (C#) SDK è parte di Azure SDK per .NET. È open source ed è basato sulle API dei piani dati di Azure Digital gemelli.

> [!NOTE]
> Per altre informazioni sulla progettazione di SDK, vedere i [principi di progettazione generali per gli SDK di Azure](https://azure.github.io/azure-sdk/general_introduction.html) e le [linee guida di progettazione .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html)specifiche.

Per usare l'SDK, includere il pacchetto NuGet **Azure. DigitalTwins. Core** con il progetto. Sarà necessaria anche la versione più recente del pacchetto **Azure. Identity** .

* In Visual Studio è possibile aggiungere pacchetti con gestione pacchetti NuGet (a cui si accede tramite *strumenti > gestione pacchetti nuget > gestire i pacchetti NuGet per la soluzione*). 
* Utilizzando lo strumento da riga di comando .NET è possibile eseguire:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Per una procedura dettagliata sull'uso delle API, vedere l' [*esercitazione: scrivere codice per un'app client*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Esempi di utilizzo di .NET SDK

Di seguito sono riportati alcuni esempi di codice che illustrano l'uso di .NET SDK.

Eseguire l'autenticazione per il servizio:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Caricare un modello ed elencare i modelli:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Creazione ed esecuzione di query sui dispositivi gemelli:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Vedere l' [*esercitazione: scrivere codice di un'app client*](tutorial-code.md) per una procedura dettagliata del codice dell'app di esempio. 

È anche possibile trovare esempi aggiuntivi nel [repository GitHub per .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Helper di serializzazione

Gli helper di serializzazione sono funzioni di supporto disponibili nell'SDK per creare o deserializzare rapidamente i dati gemelli per l'accesso alle informazioni di base. Poiché i metodi di Core SDK restituiscono i dati gemelli come JSON per impostazione predefinita, può essere utile usare queste classi helper per suddividere ulteriormente i dati gemelli.

Le classi helper disponibili sono:
* `BasicDigitalTwin`: Rappresenta i dati principali di un dispositivo gemello digitale
* `BasicRelationship`: Rappresenta i dati principali di una relazione
* `UpdateOperationUtility`: Rappresenta le informazioni sulla patch JSON usate nelle chiamate di aggiornamento
* `WriteableProperty`: Rappresenta i metadati della proprietà

##### <a name="deserialize-a-digital-twin"></a>Deserializzare un dispositivo gemello digitale

È sempre possibile deserializzare i dati gemelli usando la libreria JSON preferita, ad esempio `System.Test.Json` o `Newtonsoft.Json` . Per l'accesso di base a un dispositivo gemello, le classi helper lo rendono leggermente più pratico.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

La `BasicDigitalTwin` classe helper consente inoltre di accedere alle proprietà definite nel dispositivo gemello, tramite un `Dictionary<string, object>` . Per elencare le proprietà del dispositivo gemello, è possibile usare:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Creare un dispositivo gemello digitale

Utilizzando la `BasicDigitalTwin` classe, è possibile preparare i dati per la creazione di un'istanza del dispositivo gemello:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

Il codice precedente è equivalente alla variante "Manual" riportata di seguito:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Deserializzare una relazione

È sempre possibile deserializzare i dati delle relazioni usando la libreria JSON preferita, ad esempio `System.Test.Json` o `Newtonsoft.Json` . Per l'accesso di base a una relazione, le classi helper lo rendono leggermente più pratico.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

La `BasicRelationship` classe helper consente inoltre di accedere alle proprietà definite nella relazione, tramite un oggetto `Dictionary<string, object>` . Per elencare le proprietà, è possibile usare:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Creare una relazione

Utilizzando la `BasicRelationship` classe, è inoltre possibile preparare i dati per la creazione di relazioni in un'istanza gemella:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Creare una patch per l'aggiornamento dei dispositivi gemelli

Le chiamate di aggiornamento per i dispositivi gemelli e le relazioni usano la struttura [patch JSON](http://jsonpatch.com/) . Per creare elenchi di operazioni patch JSON, è possibile usare la `UpdateOperationsUtility` classe come illustrato di seguito.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Note generali sull'utilizzo di API/SDK

> [!NOTE]
> Si noti che durante l'anteprima, i dispositivi gemelli digitali di Azure non supportano la **condivisione di risorse tra le origini (CORS)**. Di conseguenza, se si chiama un'API REST da un'app browser, un'interfaccia di [gestione API (gestione API)](../api-management/api-management-key-concepts.md) o un connettore [Power Apps](https://docs.microsoft.com/powerapps/powerapps-overview) , è possibile che venga visualizzato un errore del criterio.
> Per risolvere questo errore, esegui una delle operazioni seguenti:
> * Rimuovere l'intestazione CORS `Access-Control-Allow-Origin` dal messaggio. Questa intestazione indica se la risposta può essere condivisa. 
> * In alternativa, creare un proxy CORS ed eseguire la richiesta dell'API REST di Azure Digital gemelli. 

L'elenco seguente fornisce informazioni aggiuntive e linee guida generali per l'uso delle API e degli SDK.

* Per usare l'SDK, creare un'istanza della `DigitalTwinsClient` classe. Il costruttore richiede credenziali che possono essere ottenute con diversi metodi di autenticazione nel `Azure.Identity` pacchetto. Per ulteriori informazioni `Azure.Identity` , vedere la relativa [documentazione dello spazio dei nomi](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true). 
* Potrebbe risultare `InteractiveBrowserCredential` utile durante l'introduzione, ma sono disponibili diverse altre opzioni, incluse le credenziali per l' [identità gestita](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true), che è possibile usare per autenticare le [funzioni di Azure configurate con MSI](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) nei dispositivi gemelli digitali di Azure. Per ulteriori informazioni su `InteractiveBrowserCredential` , vedere la relativa documentazione relativa alla [classe](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true).
* Tutte le chiamate all'API del servizio sono esposte come funzioni membro sulla `DigitalTwinsClient` classe.
* Tutte le funzioni del servizio sono disponibili in versioni sincrone e asincrone.
* Tutte le funzioni del servizio generano un'eccezione per qualsiasi stato di restituzione 400 o superiore. Assicurarsi di eseguire il wrapping delle chiamate in una `try` sezione e intercettare almeno `RequestFailedExceptions` . Per ulteriori informazioni su questo tipo di eccezione, vedere [qui](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true).
* La maggior parte dei metodi di servizio restituisce `Response<T>` o ( `Task<Response<T>>` per le chiamate asincrone), dove `T` è la classe dell'oggetto restituito per la chiamata al servizio. La [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true) classe incapsula il risultato del servizio e Visualizza i valori restituiti nel `Value` campo.  
* I metodi del servizio con risultati di paging restituiscono `Pageable<T>` o `AsyncPageable<T>` come risultati. Per ulteriori informazioni sulla `Pageable<T>` classe, vedere [qui](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview&preserve-view=true). per ulteriori informazioni su `AsyncPageable<T>` , vedere [qui](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview&preserve-view=true).
* È possibile scorrere i risultati di paging usando un `await foreach` ciclo. Per ulteriori informazioni su questo processo, vedere [qui](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* L'SDK sottostante è `Azure.Core` . Per informazioni di riferimento sull'infrastruttura e sui tipi SDK, vedere la [documentazione dello spazio dei nomi di Azure](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview&preserve-view=true) .

I metodi del servizio restituiscono oggetti fortemente tipizzati laddove possibile. Tuttavia, poiché i dispositivi gemelli digitali di Azure si basano sui modelli configurati in modo personalizzato dall'utente in fase di esecuzione (tramite i modelli DTDL caricati nel servizio), molte API del servizio accettano e restituiscono i dati gemelli in formato JSON.

## <a name="monitor-api-metrics"></a>Monitorare le metriche API

Le metriche API, ad esempio richieste, latenza e percentuale di errori, possono essere visualizzate nel [portale di Azure](https://portal.azure.com/). 

Dalla Home page del portale, cercare l'istanza di Azure Digital gemelli per recuperare i dettagli. Selezionare l'opzione **metrica** dal menu dell'istanza di Azure Digital gemelli per visualizzare la pagina *metrica* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot che mostra la pagina delle metriche per i dispositivi gemelli digitali di Azure":::

Da qui è possibile visualizzare le metriche per l'istanza e creare visualizzazioni personalizzate.

## <a name="next-steps"></a>Passaggi successivi

Vedere come usare le API per configurare un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione:
* [*Procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-scripted.md)

In alternativa, esaminare i passaggi per creare un'app client come quella usata in questa procedura:
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)
