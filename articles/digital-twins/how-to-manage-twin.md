---
title: Gestire i gemelli digitali
titleSuffix: Azure Digital Twins
description: Vedere come recuperare, aggiornare ed eliminare singoli gemelli e relazioni.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f4d9811dc288222c0a2190805a8b052cb1ae47b
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563926"
---
# <a name="manage-digital-twins"></a>Gestire i gemelli digitali

Le entità nell'ambiente sono rappresentate da dispositivi [gemelli digitali](concepts-twins-graph.md). La gestione dei dispositivi gemelli digitali può includere la creazione, la modifica e la rimozione. Per eseguire queste operazioni, è possibile usare le [**API DigitalTwins**](how-to-use-apis-sdks.md), [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione di dispositivi gemelli digitali; per lavorare con le relazioni e il [grafo gemello](concepts-twins-graph.md) nel suo complesso, vedere [*How-to: Manage The Twin Graph with Relationships*](how-to-manage-graph.md).

> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

## <a name="create-a-digital-twin"></a>Creare un dispositivo gemello digitale

Per creare un dispositivo gemello, usare il `CreateDigitalTwin` metodo nel client del servizio come segue:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Per creare un dispositivo gemello digitale, è necessario fornire:
* ID desiderato per il dispositivo gemello digitale
* [Modello](concepts-models.md) che si desidera utilizzare 

Facoltativamente, è possibile fornire i valori iniziali per tutte le proprietà del dispositivo gemello digitale. 

Il modello e i valori delle proprietà iniziali vengono forniti tramite il `initData` parametro, ovvero una stringa JSON contenente i dati rilevanti.

> [!TIP]
> Dopo la creazione o l'aggiornamento di un dispositivo gemello, è possibile che si verifichi una latenza di un massimo di 10 secondi prima che le modifiche vengano riflesse nelle [query](how-to-query-graph.md). L' `GetDigitalTwin` API, descritta [più avanti in questo articolo](#get-data-for-a-digital-twin), non riscontra questo ritardo, quindi usare la chiamata API anziché eseguire query per visualizzare i dispositivi gemelli appena creati se è necessaria una risposta immediata. 

### <a name="initialize-properties"></a>Inizializza proprietà

L'API per la creazione di dispositivi gemelli accetta un oggetto che può essere serializzato in una descrizione JSON valida delle proprietà dei dispositivi gemelli. Per una descrizione del formato JSON per un gemello, vedere [*concetti: dispositivi gemelli digitali e il grafico a gemelli*](concepts-twins-graph.md) .

È possibile creare un oggetto Parameter manualmente oppure usando una classe helper fornita. Di seguito è riportato un esempio di ogni.

#### <a name="create-twins-using-manually-created-data"></a>Creare i dispositivi gemelli usando dati creati manualmente

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Creare i dispositivi gemelli con la classe helper

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Ottenere i dati per un dispositivo gemello digitale

È possibile accedere ai dati completi di qualsiasi dispositivo gemello digitale chiamando:

```csharp
object result = await client.GetDigitalTwin(id);
```

Questa chiamata restituisce i dati gemelli come stringa JSON. 

> [!TIP]
> Quando si recupera un gemello con, vengono restituite solo le proprietà impostate almeno una volta `GetDigitalTwin` .

Per recuperare più dispositivi gemelli usando una singola chiamata API, vedere gli esempi di API di query in [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

Si consideri il modello seguente (scritto in [Digital Gemini Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) che definisce una *Luna*:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

Il risultato della chiamata `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` su un gemello di tipo *Moon*potrebbe essere simile al seguente:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Le proprietà definite del gemello digitale vengono restituite come proprietà di primo livello nel dispositivo gemello digitale. I metadati o le informazioni di sistema che non fanno parte della definizione DTDL vengono restituiti con un `$` prefisso. Le proprietà dei metadati includono:
* ID del gemello digitale in questa istanza di Azure Digital gemelli, come `$dtId` .
* `$etag`, un campo HTTP standard assegnato dal server Web
* Altre proprietà in una `$metadata` sezione. incluse le seguenti:
    - DTMI del modello del gemello digitale.
    - Stato di sincronizzazione per ogni proprietà scrivibile. Questa operazione è particolarmente utile per i dispositivi, in cui è possibile che il servizio e il dispositivo abbiano stati divergenti, ad esempio quando un dispositivo è offline. Attualmente questa proprietà si applica solo ai dispositivi fisici connessi all'hub Internet. Con i dati nella sezione dei metadati, è possibile comprendere lo stato completo di una proprietà, oltre ai timestamp dell'Ultima modifica. Per altre informazioni sullo stato di sincronizzazione, vedere [questa esercitazione sull'hub di questo](../iot-hub/tutorial-device-twins.md) strumento sulla sincronizzazione dello stato del dispositivo.
    - Metadati specifici del servizio, ad esempio dall'hub o dai dispositivi gemelli digitali di Azure. 

È possibile analizzare il JSON restituito per il gemello usando una libreria di analisi JSON di propria scelta, ad esempio `System.Text.Json` .

È anche possibile usare la classe helper di serializzazione `BasicDigitalTwin` inclusa nell'SDK, che restituirà i metadati e le proprietà del gemello di base in un modulo pre-analizzato. Esempio:

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

Per altre informazioni sulle classi helper di serializzazione, vedere [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Aggiornare un gemello digitale

Per aggiornare le proprietà di un dispositivo gemello digitale, è necessario scrivere le informazioni che si desidera sostituire nel formato di [patch JSON](http://jsonpatch.com/) . In questo modo, è possibile sostituire più proprietà contemporaneamente. Passare quindi il documento della patch JSON in un `Update` Metodo:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

> [!TIP]
> Dopo la creazione o l'aggiornamento di un dispositivo gemello, è possibile che si verifichi una latenza di un massimo di 10 secondi prima che le modifiche vengano riflesse nelle [query](how-to-query-graph.md). L' `GetDigitalTwin` API (descritta [in precedenza in questo articolo](#get-data-for-a-digital-twin)) non presenta questo ritardo, quindi usare la chiamata API invece di eseguire query per visualizzare i gemelli appena aggiornati se è necessaria una risposta immediata. 

Di seguito è riportato un esempio di codice patch JSON. Questo documento sostituisce i valori delle proprietà di *massa* e *RADIUS* del dispositivo gemello digitale a cui viene applicato.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

È possibile creare patch manualmente o usando una classe helper di serializzazione nell' [SDK](how-to-use-apis-sdks.md). Di seguito è riportato un esempio di ogni.

#### <a name="create-patches-manually"></a>Creazione manuale di patch
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Creare patch usando la classe helper

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Aggiornare le proprietà nei componenti gemelli digitali

Tenere presente che un modello può contenere componenti, in modo che sia costituito da altri modelli. 

Per applicare patch alle proprietà nei componenti di un dispositivo gemello digitale, usare la sintassi del percorso nella patch JSON:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Aggiornare il modello di un dispositivo gemello digitale

La `Update` funzione può essere usata anche per eseguire la migrazione di un dispositivo gemello digitale a un modello diverso. 

Si consideri, ad esempio, il documento di patch JSON seguente che sostituisce il campo dei metadati del gemello digitale `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Questa operazione avrà esito positivo solo se il dispositivo gemello digitale modificato dalla patch è conforme al nuovo modello. 

Prendere in considerazione gli esempi seguenti:
1. Immaginate un dispositivo gemello digitale con un modello di *foo_old*. *foo_old* definisce una *massa*di proprietà obbligatoria.
2. Il nuovo modello *foo_new* definisce una massa della proprietà e aggiunge una nuova *temperatura*della proprietà richiesta.
3. Dopo la patch, il dispositivo gemello digitale deve avere una proprietà di massa e di temperatura. 

La patch per questa situazione deve aggiornare sia il modello che la proprietà temperatura del dispositivo gemello, come indicato di seguito:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Eliminare un dispositivo gemello digitale

È possibile eliminare i dispositivi gemelli usando `DeleteDigitalTwin(ID)` . Tuttavia, è possibile eliminare un gemello solo quando non sono presenti altre relazioni. Prima di tutto è necessario eliminare tutte le relazioni. 

Di seguito è riportato un esempio di codice per:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Elimina tutti i dispositivi gemelli digitali

Per un esempio di come eliminare tutti i dispositivi gemelli contemporaneamente, scaricare l'app di esempio usata nell' [*esercitazione: esplorare le nozioni di base con un'app client di esempio*](tutorial-command-line-app.md). Il file *CommandLoop.cs* esegue questa operazione in una `CommandDeleteAllTwins` funzione.

## <a name="manage-twins-with-cli"></a>Gestire i dispositivi gemelli con CLI

I dispositivi gemelli possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital Twins È possibile trovare i comandi in [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](how-to-use-cli.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere come creare e gestire le relazioni tra i dispositivi gemelli digitali:
* [*Procedura: gestire il grafo gemello con relazioni*](how-to-manage-graph.md)