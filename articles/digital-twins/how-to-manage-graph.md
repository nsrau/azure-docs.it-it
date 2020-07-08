---
title: Gestire il grafo dei gemelli con relazioni
titleSuffix: Azure Digital Twins
description: Vedere come gestire un grafico dei dispositivi gemelli digitali connettendosi con le relazioni.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bfdf1263ccee78b57ccf79c63efcc01d95dd13c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392251"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gestire un grafico di gemelli digitali usando relazioni

Il cuore di Azure Digital gemelli è il [grafo gemello](concepts-twins-graph.md) che rappresenta l'intero ambiente. Il grafico gemello è costituito da singoli gemelli digitali connessi tramite **relazioni**.

Quando si ha un' [istanza di Azure Digital Twins](how-to-set-up-instance.md) funzionante e si è configurata [l'autenticazione](how-to-authenticate-client.md) per l'app client, è possibile usare le [**API DigitalTwins**](how-to-use-apis-sdks.md) per creare, modificare ed eliminare i dispositivi gemelli digitali e le relative relazioni in un'istanza di Azure Digital Twins. È anche possibile usare [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione delle relazioni e del grafo nel suo complesso; per lavorare con singoli dispositivi gemelli digitali, vedere [How-to: Manage Digital gemells](how-to-manage-twin.md).

## <a name="create-relationships"></a>Creare relazioni

Le relazioni descrivono il modo in cui i gemelli digitali sono connessi tra loro, che costituisce la base del grafo gemello.

Le relazioni vengono create utilizzando la `CreateRelationship` chiamata. 

Per creare una relazione, è necessario specificare:
* ID del gemello di origine (il gemello da cui ha origine la relazione)
* ID del gemello di destinazione (il gemello in cui arriva la relazione)
* Nome relazione
* ID relazione

L'ID relazione deve essere univoco all'interno del gemello di origine specificato. Non deve necessariamente essere globalmente univoco.
Per il *foo*gemello, ad esempio, ogni ID relazione specifico deve essere univoco. Tuttavia, un'altra *barra* gemella può avere una relazione in uscita che corrisponde allo stesso ID di una relazione *foo* . 

L'esempio di codice seguente illustra come aggiungere una relazione all'istanza di Azure Digital gemelli.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Per altre informazioni sulla classe helper `BasicRelationship` , vedere [How-to: Use the Azure Digital Twins API and SDKs](how-to-use-apis-sdks.md).

## <a name="list-relationships"></a>Elencare le relazioni

Per accedere all'elenco di relazioni per un dispositivo gemello specifico nel grafico, è possibile usare:

```csharp
await client.GetRelationshipsAsync(id);
```

`Azure.Pageable<T>`Viene restituito o `Azure.AsyncPageable<T>` , a seconda che si usi la versione sincrona o asincrona della chiamata.

Di seguito è riportato un esempio completo in cui viene recuperato un elenco di relazioni:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

È possibile usare le relazioni recuperate per passare ad altri gemelli nel grafo. A tale scopo, leggere il `target` campo dalla relazione restituita e usarlo come ID per la chiamata successiva a `GetDigitalTwin` . 

### <a name="find-relationships-to-a-digital-twin"></a>Trovare relazioni con un dispositivo gemello digitale

I dispositivi gemelli digitali di Azure hanno anche un'API che consente di trovare tutte le relazioni in ingresso per un dispositivo gemello specifico. Questa operazione è spesso utile per la navigazione inversa o quando si elimina un dispositivo gemello.

L'esempio di codice precedente è stato incentrato sulla ricerca di relazioni in uscita. L'esempio seguente è simile, ma trova invece le relazioni in ingresso. Li elimina anche dopo che sono stati trovati.

Si noti che le chiamate IncomingRelationship non restituiscono la versione completa

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Eliminare relazioni

È possibile eliminare relazioni usando `DeleteRelationship(source, relId);` .

Il primo parametro specifica il gemello di origine, ovvero il gemello da cui ha origine la relazione. L'altro parametro è l'ID della relazione. Sono necessari sia l'ID gemello che l'ID relazione, perché gli ID relazione sono univoci solo all'interno dell'ambito di un dispositivo gemello.

## <a name="create-a-twin-graph"></a>Creare un grafico a gemelli 

Il frammento di codice seguente usa le operazioni relative alle relazioni di questo articolo per creare un grafico gemello da gemelli digitali e relazioni.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Creare un grafico a due gemelli da un foglio di calcolo

Nei casi d'uso pratici, le gerarchie gemelle vengono spesso create dai dati archiviati in un database diverso o forse in un foglio di calcolo. In questa sezione viene illustrato il modo in cui è possibile analizzare un foglio di calcolo.

Si consideri la tabella dati seguente, che descrive un set di gemelli digitali e di relazioni da creare.

| Modello    | ID | Parent | Nome relazione | Altri dati |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| stanza    | Room10 | Floor01 | contains | … |
| stanza    | Room11 | Floor01 | contains | … |
| stanza    | Room12 | Floor01 | contains | … |
| floor    | Floor02 | | | … |
| stanza    | Room21 | Floor02 | contains | … |
| stanza    | Room22 | Floor02 | contains | … |

Il codice seguente usa l' [API Microsoft Graph](https://docs.microsoft.com/graph/overview) per leggere un foglio di calcolo e creare un grafico a gemelli di Azure Digital gemelli dai risultati.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Gestire relazioni con l'interfaccia della riga di comando

I dispositivi gemelli e le relative relazioni possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital Twins È possibile trovare i comandi in [procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli](how-to-use-cli.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'esecuzione di query su un grafo gemelli di Azure Digital gemello:
* [Concetti: linguaggio di query](concepts-query-language.md)
* [Procedura: eseguire una query sul grafico gemello](how-to-query-graph.md)