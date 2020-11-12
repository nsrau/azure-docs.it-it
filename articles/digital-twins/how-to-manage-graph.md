---
title: Gestire il grafo dei gemelli con relazioni
titleSuffix: Azure Digital Twins
description: Vedere come gestire un grafico dei dispositivi gemelli digitali connettendosi con le relazioni.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73aa6f8f6ee36aeeb41fbc54afe217ac776a4ebc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533879"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gestire un grafico di gemelli digitali usando relazioni

Il cuore di Azure Digital gemelli è il [grafo gemello](concepts-twins-graph.md) che rappresenta l'intero ambiente. Il grafo gemello è costituito da singoli gemelli digitali connessi tramite **relazioni**. 

Quando si ha un' [istanza di Azure Digital Twins](how-to-set-up-instance-portal.md) funzionante e si è configurato il codice di [autenticazione](how-to-authenticate-client.md) nell'app client, è possibile usare le [**API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) per creare, modificare ed eliminare i dispositivi gemelli digitali e le relative relazioni in un'istanza di Azure Digital gemelli. È anche possibile usare [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione delle relazioni e del grafo nel suo complesso; per lavorare con singoli dispositivi gemelli digitali, vedere [*How-to: Manage Digital gemells*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Modalità di gestione del grafo

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

È anche possibile apportare modifiche al grafo usando l'esempio di Esplora risorse di Azure Digital Twins (ADT), che consente di visualizzare i dispositivi gemelli e il grafo e usa l'SDK dietro le quinte. Questo esempio viene descritto in dettaglio nella sezione successiva.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Creare relazioni

Le relazioni descrivono il modo in cui i gemelli digitali sono connessi tra loro, che costituisce la base del grafo gemello.

Le relazioni vengono create utilizzando la `CreateOrReplaceRelationshipAsync()` chiamata. 

Per creare una relazione, è necessario specificare:
* ID del dispositivo gemello `srcId` di origine (nell'esempio di codice riportato di seguito): ID del gemello in cui ha origine la relazione.
* ID del gemello di destinazione ( `targetId` nell'esempio di codice riportato di seguito): l'ID del gemello in cui arriva la relazione.
* Un nome di relazione ( `relName` nell'esempio di codice riportato di seguito): tipo generico di relazione, simile a _Contains_.
* Un ID relazione ( `relId` nell'esempio di codice riportato di seguito): il nome specifico per la relazione, ad esempio _Relationship1_.

L'ID relazione deve essere univoco all'interno del gemello di origine specificato. Non è necessario che sia globalmente univoco.
Per il *foo* gemello, ad esempio, ogni ID relazione specifico deve essere univoco. Tuttavia, un'altra *barra* gemella può avere una relazione in uscita che corrisponde allo stesso ID di una relazione *foo* .

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
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
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

Se lo si desidera, è anche possibile creare più istanze dello stesso tipo di relazione tra gli stessi due gemelli. In questo esempio, il *gemello a* può avere due relazioni *archiviate* diverse con il *gemello B* , purché le relazioni abbiano ID relazione diversi.

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
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
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

I dispositivi gemelli digitali di Azure hanno anche un'API per trovare tutte le relazioni * in *ingresso* a un determinato dispositivo gemello. Questa operazione è spesso utile per la navigazione inversa o quando si elimina un dispositivo gemello.

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
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

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
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
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
await DeleteRelationship(client, srcId, relId);
```

## <a name="runnable-twin-graph-sample"></a>Esempio di grafico a gemello eseguibile

Il frammento di codice eseguibile seguente usa le operazioni relative alle relazioni di questo articolo per creare un grafico gemello da gemelli digitali e relazioni.

### <a name="set-up-the-runnable-sample"></a>Configurare l'esempio eseguibile

Il frammento di codice usa il [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) e [*Floor.jsnelle*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definizioni di modello da [*esercitazione: esplorare i dispositivi gemelli digitali di Azure con un'app client di esempio*](tutorial-command-line-app.md). È possibile usare questi collegamenti per passare direttamente ai file o scaricarli come parte del progetto di esempio end-to-end completo [qui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Prima di eseguire l'esempio, eseguire le operazioni seguenti:
1. Scaricare i file del modello, inserirli nel progetto e sostituire i `<path-to>` segnaposto nel codice riportato di seguito per indicare al programma dove trovarli.
2. Sostituire il segnaposto `<your-instance-hostname>` con il nome host dell'istanza di Azure Digital gemelli.
3. Aggiungere i pacchetti seguenti al progetto:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

È inoltre necessario configurare le credenziali locali se si desidera eseguire l'esempio direttamente. La sezione successiva illustra questa procedura.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Eseguire l'esempio

Dopo aver completato i passaggi precedenti, è possibile eseguire direttamente il codice di esempio seguente.

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

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
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
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

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
    }
}
```

Ecco l'output della console del programma precedente: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Output della console che mostra i dettagli del dispositivo gemello, le relazioni in ingresso e in uscita dei dispositivi gemelli." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Il grafo gemello è un concetto di creazione di relazioni tra due gemelli. Per visualizzare la rappresentazione visiva del grafo gemello, vedere la sezione [_Visualization *](how-to-manage-graph.md#visualization) di questo articolo. 

### <a name="create-a-twin-graph-from-a-csv-file"></a>Creare un grafico gemello da un file CSV

Nei casi d'uso pratici, le gerarchie gemelle vengono spesso create dai dati archiviati in un database diverso o eventualmente in un foglio di calcolo o in un file CSV. In questa sezione viene illustrato come leggere i dati da un file CSV e creare un grafico gemello.

Si consideri la tabella dati seguente, che descrive un set di gemelli e relazioni digitali.

|  ID modello    | ID gemello (deve essere univoco) | Nome relazione  | ID gemello di destinazione  | Dati init gemelli |
| --- | --- | --- | --- | --- |
| dtmi: esempio: Floor; 1    | Floor1 | contains | Room1 | |
| dtmi: esempio: Floor; 1    | Floor0 | contains | Room0 | |
| dtmi: esempio: Room; 1    | Room1 | | | {"Temperature": 80} |
| dtmi: esempio: Room; 1    | Room0 | | | {"Temperature": 70} |

Un modo per ottenere questi dati nei dispositivi gemelli digitali di Azure consiste nel convertire la tabella in un file CSV e scrivere codice per interpretare il file nei comandi per creare i gemelli e le relazioni. Nell'esempio di codice seguente viene illustrata la lettura dei dati dal file CSV e la creazione di un grafico a gemelli nei dispositivi gemelli digitali di Azure.

Nel codice riportato di seguito, il file CSV è denominato *data.csv* ed è presente un segnaposto che rappresenta il **nome host** dell'istanza di Azure Digital gemelli. Nell'esempio vengono inoltre utilizzati diversi pacchetti che è possibile aggiungere al progetto per agevolare il processo.

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'esecuzione di query su un grafo gemelli di Azure Digital gemello:
* [*Concetti: linguaggio di query*](concepts-query-language.md)
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)