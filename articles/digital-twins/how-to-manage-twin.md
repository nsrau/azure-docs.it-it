---
title: Gestire i gemelli digitali
titleSuffix: Azure Digital Twins
description: Vedere come recuperare, aggiornare ed eliminare singoli gemelli e relazioni.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 58ee064d4946442bff70e97d56a68080333e2197
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426130"
---
# <a name="manage-digital-twins"></a>Gestire i gemelli digitali

Le entità nell'ambiente sono rappresentate da dispositivi [gemelli digitali](concepts-twins-graph.md). La gestione dei dispositivi gemelli digitali può includere la creazione, la modifica e la rimozione. Per eseguire queste operazioni, è possibile usare le [**API DigitalTwins**](how-to-use-apis-sdks.md), [.NET (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione di dispositivi gemelli digitali; per lavorare con le relazioni e il [grafo gemello](concepts-twins-graph.md) nel suo complesso, vedere [*How-to: Manage The Twin Graph with Relationships*](how-to-manage-graph.md).

> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

## <a name="create-a-digital-twin"></a>Creare un dispositivo gemello digitale

Per creare un dispositivo gemello, usare il `CreateDigitalTwin()` metodo nel client del servizio come segue:

```csharp
await client.CreateDigitalTwinAsync("myTwinId", initData);
```

Per creare un dispositivo gemello digitale, è necessario fornire:
* ID desiderato per il dispositivo gemello digitale
* [Modello](concepts-models.md) che si desidera utilizzare

Facoltativamente, è possibile fornire i valori iniziali per tutte le proprietà del dispositivo gemello digitale. 

Il modello e i valori delle proprietà iniziali vengono forniti tramite il `initData` parametro, ovvero una stringa JSON contenente i dati rilevanti. Per ulteriori informazioni sulla strutturazione di questo oggetto, passare alla sezione successiva.

> [!TIP]
> Dopo la creazione o l'aggiornamento di un dispositivo gemello, è possibile che si verifichi una latenza di un massimo di 10 secondi prima che le modifiche vengano riflesse nelle [query](how-to-query-graph.md). L' `GetDigitalTwin` API, descritta [più avanti in questo articolo](#get-data-for-a-digital-twin), non riscontra questo ritardo, quindi se è necessaria una risposta immediata, usare la chiamata API anziché eseguire query per visualizzare i dispositivi gemelli appena creati. 

### <a name="initialize-model-and-properties"></a>Inizializzare il modello e le proprietà

L'API per la creazione di dispositivi gemelli accetta un oggetto che viene serializzato in una descrizione JSON valida delle proprietà dei dispositivi gemelli. Per una descrizione del formato JSON per un gemello, vedere [*concetti: dispositivi gemelli digitali e il grafico a gemelli*](concepts-twins-graph.md) . 

In primo luogo, è possibile creare un oggetto dati per rappresentare il gemello e i relativi dati della proprietà. È quindi possibile usare `JsonSerializer` per passare una versione serializzata di questo oggetto nella chiamata API per il `initdata` parametro, come indicato di seguito:

```csharp
await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```
È possibile creare un oggetto Parameter manualmente oppure usando una classe helper fornita. Di seguito è riportato un esempio di ogni.

#### <a name="create-twins-using-manually-created-data"></a>Creare i dispositivi gemelli usando dati creati manualmente

Senza l'uso di alcuna classe helper personalizzata, è possibile rappresentare le proprietà di un gemello in un `Dictionary<string, object>` oggetto, dove `string` è il nome della proprietà e `object` è un oggetto che rappresenta la proprietà e il relativo valore.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Creare i dispositivi gemelli con la classe helper

La classe helper di `BasicDigitalTwin` consente di archiviare direttamente i campi di proprietà in un oggetto "gemello". È comunque possibile creare l'elenco di proprietà usando un `Dictionary<string, object>` oggetto, che può quindi essere aggiunto direttamente all'oggetto gemello `CustomProperties` .

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwinAsync("myRoomId", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` gli oggetti vengono inclusi in un `Id` campo. È possibile lasciare vuoto questo campo, ma se si aggiunge un valore ID, è necessario che corrisponda al parametro ID passato alla `CreateDigitalTwin()` chiamata. Ad esempio:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Ottenere i dati per un dispositivo gemello digitale

È possibile accedere ai dettagli di qualsiasi dispositivo gemello digitale chiamando il `GetDigitalTwin()` metodo come segue:

```csharp
object result = await client.GetDigitalTwin(id);
```
Questa chiamata restituisce i dati gemelli come stringa JSON. Ecco un esempio di come usare questa procedura per visualizzare i dettagli dei dispositivi gemelli:

```csharp
Response<string> res = client.GetDigitalTwin("myRoomId");
twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
  if (twin.CustomProperties.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
Quando si recupera un gemello con il metodo, vengono restituite solo le proprietà impostate almeno una volta `GetDigitalTwin()` .

>[!TIP]
>`displayName`Per un dispositivo gemello fa parte dei metadati del modello, pertanto non verrà visualizzato quando si recuperano i dati per l'istanza del dispositivo gemello. Per visualizzare questo valore, è possibile [recuperarlo dal modello](how-to-manage-model.md#retrieve-models).

Per recuperare più dispositivi gemelli usando una singola chiamata API, vedere gli esempi di API di query in [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

Si consideri il modello seguente (scritto in [Digital Gemini Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) che definisce una *Luna*:

```json
{
    "@id": "dtmi:example:Moon;1",
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
Il risultato della chiamata `object result = await client.GetDigitalTwinAsync("my-moon");` su un gemello di tipo *Moon*potrebbe essere simile al seguente:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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
* `$etag`, un campo HTTP standard assegnato dal server Web.
* Altre proprietà in una `$metadata` sezione. Sono inclusi:
    - DTMI del modello del gemello digitale.
    - Stato di sincronizzazione per ogni proprietà scrivibile. Questa operazione è particolarmente utile per i dispositivi, in cui è possibile che il servizio e il dispositivo abbiano stati divergenti, ad esempio quando un dispositivo è offline. Attualmente questa proprietà si applica solo ai dispositivi fisici connessi all'hub Internet. Con i dati nella sezione dei metadati, è possibile comprendere lo stato completo di una proprietà, oltre ai timestamp dell'Ultima modifica. Per altre informazioni sullo stato di sincronizzazione, vedere [questa esercitazione sull'hub di questo](../iot-hub/tutorial-device-twins.md) strumento sulla sincronizzazione dello stato del dispositivo.
    - Metadati specifici del servizio, ad esempio dall'hub o dai dispositivi gemelli digitali di Azure. 

È possibile analizzare il JSON restituito per il gemello usando una libreria di analisi JSON di propria scelta, ad esempio `System.Text.Json` .

È anche possibile usare la classe helper di serializzazione `BasicDigitalTwin` inclusa nell'SDK, che restituirà i metadati e le proprietà del gemello di base in un modulo pre-analizzato. Esempio:

```csharp
Response<string> res = client.GetDigitalTwin(twin_Id);
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

Per aggiornare le proprietà di un dispositivo gemello digitale, è necessario scrivere le informazioni che si desidera sostituire nel formato di [patch JSON](http://jsonpatch.com/) . In questo modo, è possibile sostituire più proprietà contemporaneamente. Passare quindi il documento della patch JSON in un `UpdateDigitalTwin()` Metodo:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Una chiamata patch può aggiornare tutte le proprietà di un singolo gemello come si preferisce (anche tutte). Se è necessario aggiornare le proprietà tra più dispositivi gemelli, sarà necessaria una chiamata di aggiornamento separata per ogni dispositivo gemello.

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

await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
Console.WriteLine("Updated twin properties");
FetchAndPrintTwin(twin_Id, client);
}
```

#### <a name="create-patches-using-the-helper-class"></a>Creare patch usando la classe helper

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twin_Id, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Aggiornare le proprietà nei componenti gemelli digitali

Tenere presente che un modello può contenere componenti, in modo che sia costituito da altri modelli. 

Per applicare patch alle proprietà nei componenti di un dispositivo gemello digitale, è possibile usare la sintassi del percorso nella patch JSON:

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

La `UpdateDigitalTwin()` funzione può essere usata anche per eseguire la migrazione di un dispositivo gemello digitale a un modello diverso. 

Si consideri, ad esempio, il documento di patch JSON seguente che sostituisce il campo dei metadati del gemello digitale `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

Questa operazione avrà esito positivo solo se il dispositivo gemello digitale modificato dalla patch è conforme al nuovo modello. 

Si consideri l'esempio seguente:
1. Immaginate un dispositivo gemello digitale con un modello di *foo_old*. *foo_old* definisce una *massa*di proprietà obbligatoria.
2. Il nuovo modello *foo_new* definisce una massa della proprietà e aggiunge una nuova *temperatura*della proprietà richiesta.
3. Dopo la patch, il dispositivo gemello digitale deve avere una proprietà di massa e di temperatura. 

La patch per questa situazione deve aggiornare sia il modello che la proprietà temperatura del dispositivo gemello, come indicato di seguito:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Gestire le chiamate di aggiornamento in conflitto

I dispositivi gemelli digitali di Azure garantiscono che tutte le richieste in ingresso vengano elaborate una dopo l'altra. Ciò significa che, anche se più funzioni tentano di aggiornare la stessa proprietà in un dispositivo gemello allo stesso tempo, non è **necessario** scrivere codice di blocco esplicito per gestire il conflitto.

Questo comportamento si basa su un singolo dispositivo. 

Si supponga, ad esempio, uno scenario in cui queste tre chiamate arrivano allo stesso tempo: 
*   Scrivi proprietà A in *Twin1*
*   Scrivere la proprietà B in *Twin1*
*   Scrivi proprietà A in *Twin2*

Le due chiamate che modificano *Twin1* vengono eseguite una dopo l'altra e i messaggi delle modifiche vengono generati per ogni modifica. La chiamata a Modify *Twin2* può essere eseguita simultaneamente senza conflitti, non appena arriva.

## <a name="delete-a-digital-twin"></a>Eliminare un dispositivo gemello digitale

È possibile eliminare i dispositivi gemelli usando il `DeleteDigitalTwin()` metodo. Tuttavia, è possibile eliminare un gemello solo quando non sono presenti altre relazioni. Quindi, eliminare prima le relazioni in ingresso e in uscita del dispositivo gemello.

Di seguito è riportato un esempio del codice per eliminare i gemelli e le relative relazioni:

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
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Elimina tutti i dispositivi gemelli digitali

Per un esempio di come eliminare tutti i dispositivi gemelli contemporaneamente, scaricare l'app di esempio usata nella [_Tutorial: esplorare le nozioni di base con un'app client di esempio *](tutorial-command-line-app.md). Il file *CommandLoop.cs* esegue questa operazione in una `CommandDeleteAllTwins()` funzione.

## <a name="manage-twins-using-runnable-code-sample"></a>Gestire i dispositivi gemelli usando un esempio di codice eseguibile

È possibile usare l'esempio di codice eseguibile riportato di seguito per creare un dispositivo gemello, aggiornarne i dettagli ed eliminare il dispositivo gemello. 

Il frammento di codice usa il [Room.jsnella](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definizione del modello da [*esercitazione: esplorare i dispositivi gemelli digitali di Azure con un'app client di esempio*](tutorial-command-line-app.md). È possibile usare questo collegamento per passare direttamente al file o scaricarlo come parte del progetto di esempio end-to-end completo [qui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Sostituire il segnaposto `<your-instance-hostname>` con i dettagli dell'istanza di Azure Digital gemelli ed eseguire l'esempio.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            BasicDigitalTwin twin = new BasicDigitalTwin();
            var typeList = new List<string>();
            string twin_Id = "myRoomId";
            string dtdl = File.ReadAllText("Room.json");
            typeList.Add(dtdl);
            // Upload the model to the service
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            await client.CreateModelsAsync(typeList);
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.CustomProperties = props;
            await client.CreateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            Console.WriteLine("Twin created successfully");
            twin = FetchAndPrintTwin(twin_Id, client);
            List<object> twinData = new List<object>();
            twinData.Add(new Dictionary<string, object>() 
            {
                { "op", "add"},
                { "path", "/Temperature"},
                { "value", 25.0}
            });

            await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
            Console.WriteLine("Updated Twin Properties");
            FetchAndPrintTwin(twin_Id, client);
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.CustomProperties.Keys)
            {
                if (twin.CustomProperties.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
                FetchAndPrintTwin(id, client);
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        public static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Ecco l'output della console del programma precedente: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Output della console che mostra che il dispositivo gemello è stato creato, aggiornato ed eliminato" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="manage-twins-with-cli"></a>Gestire i dispositivi gemelli con CLI

I dispositivi gemelli possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital Twins I comandi sono disponibili in [_How: usare l'interfaccia della riga di comando di Azure Digital gemelli *](how-to-use-cli.md).

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="view-all-digital-twins"></a>Visualizza tutti i dispositivi gemelli digitali

Per visualizzare tutti i dispositivi gemelli digitali nell'istanza, usare una [query](how-to-query-graph.md). È possibile eseguire una query con le [API di query](how-to-use-apis-sdks.md) o i [comandi dell'interfaccia](how-to-use-cli.md)della riga di comando.

Di seguito è riportato il corpo della query di base che restituirà un elenco di tutti i dispositivi gemelli digitali nell'istanza:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="next-steps"></a>Passaggi successivi

Vedere come creare e gestire le relazioni tra i dispositivi gemelli digitali:
* [*Procedura: gestire il grafo gemello con relazioni*](how-to-manage-graph.md)