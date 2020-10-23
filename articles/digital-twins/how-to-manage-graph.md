---
title: Gestire il grafo dei gemelli con relazioni
titleSuffix: Azure Digital Twins
description: Vedere come gestire un grafico dei dispositivi gemelli digitali connettendosi con le relazioni.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6d197c8853521e0fb0c6e247ad05a046da559454
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427914"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gestire un grafico di gemelli digitali usando relazioni

Il cuore di Azure Digital gemelli è il [grafo gemello](concepts-twins-graph.md) che rappresenta l'intero ambiente. Il grafo gemello è costituito da singoli gemelli digitali connessi tramite **relazioni**. 

Quando si ha un' [istanza di Azure Digital Twins](how-to-set-up-instance-portal.md) funzionante e si è configurato il codice di [autenticazione](how-to-authenticate-client.md) nell'app client, è possibile usare le [**API DigitalTwins**](how-to-use-apis-sdks.md) per creare, modificare ed eliminare i dispositivi gemelli digitali e le relative relazioni in un'istanza di Azure Digital gemelli. È anche possibile usare [.NET (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione delle relazioni e del grafo nel suo complesso; per lavorare con singoli dispositivi gemelli digitali, vedere [*How-to: Manage Digital gemells*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Creare relazioni

Le relazioni descrivono il modo in cui i gemelli digitali sono connessi tra loro, che costituisce la base del grafo gemello.

Le relazioni vengono create utilizzando la `CreateRelationship()` chiamata. 

Per creare una relazione, è necessario specificare:
* ID del dispositivo gemello `srcId` di origine (nell'esempio di codice riportato di seguito): ID del gemello in cui ha origine la relazione.
* ID del gemello di destinazione ( `targetId` nell'esempio di codice riportato di seguito): l'ID del gemello in cui arriva la relazione.
* Un nome di relazione ( `relName` nell'esempio di codice riportato di seguito): tipo generico di relazione, simile a _Contains_.
* Un ID relazione ( `relId` nell'esempio di codice riportato di seguito): il nome specifico per la relazione, ad esempio _Relationship1_.

L'ID relazione deve essere univoco all'interno del gemello di origine specificato. Non è necessario che sia globalmente univoco.
Per il *foo*gemello, ad esempio, ogni ID relazione specifico deve essere univoco. Tuttavia, un'altra *barra* gemella può avere una relazione in uscita che corrisponde allo stesso ID di una relazione *foo* .

L'esempio di codice seguente illustra come creare una relazione nell'istanza di Azure Digital gemelli.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
Nel metodo Main è ora possibile chiamare la `CreateRelationship()` funzione per creare una relazione _Contains_ come la seguente: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Se si desidera creare più relazioni, è possibile ripetere le chiamate allo stesso metodo, passando tipi di relazione diversi nell'argomento. 

Per altre informazioni sulla classe helper `BasicRelationship` , vedere [*How-to: Use the Azure Digital Twins API and SDKs*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Creare più relazioni tra i dispositivi gemelli

Le relazioni possono essere classificate come una delle seguenti: 

* Relazioni in uscita: relazioni appartenenti a questo gemello che puntano verso l'esterno per connetterle ad altri dispositivi gemelli. Il `GetRelationshipsAsync()` metodo viene usato per ottenere le relazioni in uscita di un dispositivo gemello.
* Relazioni in ingresso: relazioni appartenenti ad altri gemelli che puntano a questo gemello per creare un collegamento "in ingresso". Il `GetIncomingRelationshipsAsync()` metodo viene usato per ottenere le relazioni in ingresso di un dispositivo gemello.

Non esiste alcuna restrizione al numero di relazioni che è possibile avere tra due gemelli. è possibile avere tutte le relazioni tra i dispositivi gemelli. 

Ciò significa che è possibile esprimere più tipi diversi di relazioni tra due gemelli in una sola volta. Ad esempio, *il gemello A* può avere una relazione *archiviata* e una relazione *prodotta* con il *gemello B*.

Se lo si desidera, è anche possibile creare più istanze dello stesso tipo di relazione tra gli stessi due gemelli. In questo esempio, il *gemello a* può avere due relazioni *archiviate* diverse con il *gemello B*, purché le relazioni abbiano ID relazione diversi.

## <a name="list-relationships"></a>Elencare le relazioni

Per accedere all'elenco delle relazioni in **uscita** per un determinato gemello nel grafico, è possibile usare il `GetRelationships()` metodo come segue:

```csharp
await client.GetRelationships()
```

`Azure.Pageable<T>`Viene restituito o `Azure.AsyncPageable<T>` , a seconda che si usi la versione sincrona o asincrona della chiamata.

Di seguito è riportato un esempio in cui viene recuperato un elenco di relazioni:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
È ora possibile chiamare questo metodo per vedere le relazioni in uscita dei dispositivi gemelli come segue:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
È possibile usare le relazioni recuperate per passare ad altri gemelli nel grafo. A tale scopo, leggere il `target` campo dalla relazione restituita e usarlo come ID per la chiamata successiva a `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Trovare le relazioni in ingresso a un dispositivo gemello digitale

I dispositivi gemelli digitali di Azure hanno anche un'API per trovare tutte le relazioni * in*ingresso*a un determinato dispositivo gemello. Questa operazione è spesso utile per la navigazione inversa o quando si elimina un dispositivo gemello.

L'esempio di codice precedente è stato incentrato sulla ricerca di relazioni in uscita da un gemello. L'esempio seguente è strutturato in modo analogo, ma trova invece le relazioni in *ingresso* al dispositivo gemello.

Si noti che le `IncomingRelationship` chiamate non restituiscono il corpo completo della relazione.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

È ora possibile chiamare questo metodo per vedere le relazioni in ingresso dei dispositivi gemelli come segue:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Elencare tutte le proprietà e le relazioni dei dispositivi gemelli

Usando i metodi precedenti per elencare le relazioni in uscita e in ingresso in un dispositivo gemello, è possibile creare un metodo che stampa le informazioni complete sui dispositivi gemelli, incluse le proprietà del gemello e entrambi i tipi di relazione. Ecco un metodo di esempio, denominato `FetchAndPrintTwinAsync()` , che illustra come eseguire questa operazione.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

È ora possibile chiamare questa funzione nel metodo Main, come indicato di seguito: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Eliminare relazioni

Il primo parametro specifica il gemello di origine, ovvero il gemello da cui ha origine la relazione. L'altro parametro è l'ID della relazione. Sono necessari sia l'ID gemello che l'ID relazione, perché gli ID relazione sono univoci solo all'interno dell'ambito di un dispositivo gemello.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

È ora possibile chiamare questo metodo per eliminare una relazione come la seguente:

```csharp
await DeleteRelationship(client, srcId, $"{targetId}-contains->{srcId}");
```
## <a name="create-a-twin-graph"></a>Creare un grafico a gemelli 

Il frammento di codice eseguibile seguente usa le operazioni relative alle relazioni di questo articolo per creare un grafico gemello da gemelli digitali e relazioni.

Il frammento di codice usa il [Room.js](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) e [Floor.jsnelle](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definizioni di modello da [*esercitazione: esplorare i dispositivi gemelli digitali di Azure con un'app client di esempio*](tutorial-command-line-app.md). È possibile usare questi collegamenti per passare direttamente ai file o scaricarli come parte del progetto di esempio end-to-end completo [qui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

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
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine($"Upload a model");
            BasicDigitalTwin twin = new BasicDigitalTwin();
            var typeList = new List<string>();
            string srcId = "myRoomID";
            string targetId = "myFloorID";
            string dtdl = File.ReadAllText("Room.json");
            string dtdl1 = File.ReadAllText("Floor.json");
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the model to the service

            await client.CreateModelsAsync(typeList);

            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.CustomProperties = props;

            await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            // Creating twin data for second twin
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Floor;1";
            // Initialize properties
            Dictionary<string, object> props1 = new Dictionary<string, object>();
            props1.Add("Capacity", 5.0);
            twin.CustomProperties = props1;
            await client.CreateDigitalTwinAsync(targetId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            Console.WriteLine();
            Console.WriteLine("Deleting existing relationships to the twin");
            Console.WriteLine();
            await DeleteRelationship(client, srcId);
            Console.WriteLine("Twin created successfully");
            await CreateRelationship(client, srcId, targetId, "contains");
            await CreateRelationship(client, srcId, targetId, "has");
            Console.WriteLine();
            Console.WriteLine("Printing srcId - Outgoing relationships");
            Console.WriteLine();
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Printing targetId - Incoming relationships");
            Console.WriteLine();
            await FetchAndPrintTwinAsync(targetId, client);

        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId)
        {
            List<BasicRelationship> lists = await FindOutgoingRelationshipsAsync(client, srcId);
            foreach(BasicRelationship rel in lists) {
                await client.DeleteRelationshipAsync(srcId, rel.Id);
            }
            
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{targetId}-{relName}->{srcId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel.RelationshipId);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

    }
}
```

Ecco l'output della console del programma precedente: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Output della console che mostra i dettagli del dispositivo gemello, le relazioni in ingresso e in uscita dei dispositivi gemelli." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Il grafo gemello è un concetto di creazione di relazioni tra due gemelli. Per visualizzare la rappresentazione visiva del grafo gemello, vedere la sezione [_Visualization *](how-to-manage-graph.md#visualization) di questo articolo. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Creare un grafico a due gemelli da un foglio di calcolo

Nei casi d'uso pratici, le gerarchie gemelle vengono spesso create dai dati archiviati in un database diverso o forse in un foglio di calcolo. In questa sezione viene illustrato il modo in cui è possibile analizzare un foglio di calcolo.

Si consideri la tabella dati seguente, che descrive un set di gemelli digitali e di relazioni da creare.

| ID modello| ID gemello (deve essere univoco) | Nome relazione | ID gemello di destinazione | Dati init gemelli |
| --- | --- | --- | --- | --- |
| dtmi: esempio: Floor; 1 | Floor1 |  contains | Room1 |{"Temperature": 80, "umidità": 60}
| dtmi: esempio: Floor; 1 | Floor0 |  has      | Room0 |{"Temperature": 70, "umidità": 30}
| dtmi: esempio: Room; 1  | Room1 | 
| dtmi: esempio: Room; 1  | Room0 |

Il codice seguente usa l' [API Microsoft Graph](/graph/overview) per leggere un foglio di calcolo e creare un grafico a gemelli di Azure Digital gemelli dai risultati.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateDigitalTwinAsync(sourceId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Gestire relazioni con l'interfaccia della riga di comando

I dispositivi gemelli e le relative relazioni possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital Twins È possibile trovare i comandi in [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](how-to-use-cli.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'esecuzione di query su un grafo gemelli di Azure Digital gemello:
* [*Concetti: linguaggio di query*](concepts-query-language.md)
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)