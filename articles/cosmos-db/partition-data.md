---
title: Partizionamento e aumento del numero di istanze in Azure Cosmos DB | Microsoft Docs
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, sulla configurazione del partizionamento e delle chiavi di partizione e su come scegliere la chiave di partizione corretta per l&quot;applicazione.
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: cd3b13b9988f51fd3755ced48714fdc18cf1ea3c
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="how-to-partition-and-scale-in-azure-cosmos-db"></a>Come eseguire il partizionamento e il ridimensionamento in Azure Cosmos DB

[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database multimodello con distribuzione globale progettato per ottenere prestazioni rapide e prevedibili e per eseguire facilmente il ridimensionamento in base alla crescita dell'applicazione. Questo articolo offre una panoramica del funzionamento del partizionamento in Azure Cosmos DB e descrive come configurare contenitori di Azure Cosmos DB per ridimensionare le applicazioni in modo efficace.

Il partizionamento e le chiavi di partizione sono illustrati anche in questo video di Azure Friday con Scott Hanselman e Shireesh Thota, responsabile principale della progettazione di Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB
In Azure Cosmos DB è possibile archiviare dati ed eseguire query senza schema con tempi di risposta nell'ordine di millisecondi su qualsiasi scala. Cosmos DB offre contenitori per l'archiviazione di dati denominati **raccolte (per i documenti), grafici o tabelle**. I contenitori sono risorse logiche e possono comprendere una o più partizioni fisiche o server. Il numero di partizioni è determinato da Cosmos DB in base allo spazio di archiviazione e alla velocità effettiva con provisioning del contenitore. Ogni partizione in Cosmos DB ha una quantità fissa di archiviazione supportata da unità SSD associata e viene replicata per la disponibilità elevata. Le partizioni vengono completamente gestite da Azure Cosmos DB e non è necessario scrivere codice complesso o gestire le partizioni. I contenitori di Cosmos DB sono illimitati in termini di risorse di archiviazione e di velocità effettiva. 

![orizzontale](./media/introduction/azure-cosmos-db-partitioning.png) 

Il partizionamento è trasparente per l'applicazione. Cosmos DB supporta letture e scritture veloci, query, logica transazionale, livelli di coerenza e controllo di accesso con granularità fine tramite metodi/API per una singola risorsa di contenitore. Il servizio gestisce la distribuzione dei dati tra le partizioni e il routing delle richieste di query alla partizione corretta. 

Per il partizionamento, ogni elemento deve avere una chiave di partizione e una chiave di riga che lo identificano in modo univoco. La chiave di partizione funge da partizione logica per i dati e fornisce a Cosmos DB un limite naturale per la distribuzione dei dati tra partizioni. In sintesi, il partizionamento in Azure Cosmos DB funziona nel modo seguente:

* Si esegue il provisioning di un contenitore Cosmos DB con velocità effettiva di `T` richieste/s
* In background Cosmos DB effettua il provisioning delle partizioni necessarie per gestire `T` richieste/s. Se `T` è maggiore della velocità effettiva massima per partizione `t`, Cosmos DB esegue il provisioning di `N` = `T/t` partizioni
* Cosmos DB alloca lo spazio degli hash delle chiavi di partizione in modo uniforme tra le `N` partizioni. Ogni partizione fisica ospita quindi 1-N valori di chiave di partizione (partizioni logiche)
* Quando una partizione fisica `p` raggiunge il limite di archiviazione, Cosmos DB suddivide `p` in due nuove partizioni `p1` e `p2` e distribuisce i valori corrispondenti a circa la metà di chiavi per ciascuna delle partizioni. Questa operazione di suddivisione è invisibile all'applicazione.
* Analogamente, quando si esegue il provisioning di una velocità effettiva superiore alla velocità effettiva `t*N`, Cosmos DB suddivide una o più delle partizioni per supportare la velocità effettiva maggiore

La semantica delle chiavi di partizione è leggermente diversa in modo da corrispondere alla semantica di ogni API, come illustrato nella tabella seguente:

| API | Chiave di partizione | Chiave di riga |
| --- | --- | --- |
| DocumentDB | percorso personalizzato della chiave di partizione | `id` fisso | 
| MongoDB | chiave di partizione personalizzata  | `_id` fisso | 
| Grafico | proprietà chiave di partizione personalizzata | `id` fisso | 
| Tabella | `PartitionKey` fisso | `RowKey` fisso | 

Cosmos DB usa il partizionamento basato su hash. Quando si scrive un elemento, Cosmos DB esegue l'hashing del valore della chiave di partizione e usa il risultato con hash per determinare la partizione in cui archiviare l'elemento. Cosmos DB archivia tutti gli elementi con la stessa chiave di partizione nella stessa partizione fisica. La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. È necessario scegliere un nome proprietà che contenga un'ampia gamma di valori e abbia anche modelli di accesso.

> [!NOTE]
> È consigliabile avere una chiave di partizione con molti valori distinti (100-1000 come minimo).
>

I contenitori di Azure Cosmos DB possono essere creati come "fissi" o "illimitati". I contenitori a dimensione fissa hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR/s. Alcune API consentono di omettere la chiave di partizione per i contenitori a dimensione fissa. Per creare un contenitore illimitato è necessario specificare una velocità effettiva minima di 2500 UR/s.

## <a name="partitioning-and-provisioned-throughput"></a>Partizionamento e velocità effettiva con provisioning
Cosmos DB è progettato per prestazioni prevedibili. Quando si crea un contenitore, la velocità effettiva viene riservata in termini di **[unità richiesta](request-units.md) (UR) al secondo, con un potenziale add-on per UR al minuto**. A ogni richiesta viene assegnato un addebito delle unità richiesta proporzionato alla quantità di risorse di sistema, come CPU, memoria e I/O usati dall'operazione. La lettura di un documento di 1 KB con coerenza di sessione usa un'unità richiesta. Un'operazione di lettura corrisponde a 1 RU indipendentemente dal numero di elementi archiviati o dal numero di richieste simultanee in esecuzione contemporaneamente. Elementi di dimensioni maggiori richiedono più unità richiesta a seconda delle dimensioni. Se si conoscono le dimensioni delle entità e il numero di letture che è necessario supportare per l'applicazione, è possibile eseguire il provisioning della quantità esatta di velocità effettiva necessaria per le esigenze di lettura dell'applicazione. 

> [!NOTE]
> Per ottenere la velocità effettiva totale del contenitore è necessario scegliere una chiave di partizione che consenta di distribuire in modo uniforme le richieste tra alcuni valori distinti della chiave di partizione.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="working-with-the-azure-cosmos-db-apis"></a>Uso delle API di Azure Cosmos DB
È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per creare contenitori e ridimensionarli in qualsiasi momento. Questa sezione illustra come creare contenitori e specificare la definizione di velocità effettiva e chiave di partizione in ognuna delle API supportate.

### <a name="documentdb-api"></a>API di DocumentDB
L'esempio seguente illustra come creare un contenitore (raccolta) usando l'API di DocumentDB. Per informazioni più dettagliate, vedere [Partizionamento con l'API di DocumentDB](partition-data.md).

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

È possibile leggere un elemento (documento) tramite il metodo `GET` nell'API REST oppure usando `ReadDocumentAsync` in uno degli SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>API di MongoDB
Con l'API di MongoDB è possibile creare una raccolta con partizionamento orizzontale usando il proprio strumento, driver o SDK preferito. In questo esempio verrà usato Mongo Shell per la creazione della raccolta.

In Mongo Shell:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Risultati:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>API di tabella

Con l'API di tabella si specifica la velocità effettiva per le tabelle nella configurazione appSettings dell'applicazione:

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

Si crea quindi una tabella usando l'SDK di archiviazione tabelle di Azure. La chiave di partizione viene creata implicitamente come valore `PartitionKey`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

È possibile recuperare una singola entità tramite il frammento seguente:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Vedere lo [sviluppo con l'API di tabella](tutorial-develop-table-dotnet.md) per altri dettagli.

### <a name="graph-api"></a>API Graph

Con l'API Graph è necessario usare il portale di Azure o l'interfaccia della riga di comando per creare contenitori. Essendo Azure Cosmos DB multimodello, in alternativa è possibile usare uno degli altri modelli per creare e ridimensionare il contenitore di grafici.

È possibile leggere qualsiasi vertice o arco usando la chiave di partizione e l'ID in Gremlin. Per un grafico con area ("USA") come chiave di partizione e "Seattle" come chiave di riga, ad esempio, è possibile trovare un vertice usando la sintassi seguente:

```
g.V(['USA', 'Seattle'])
```

Allo stesso modo è possibile fare riferimento a un arco usando la chiave di partizione e la chiave di riga.

```
g.E(['USA', 'I5'])
```

Vedere il [supporto di Gremlin per Cosmos DB](gremlin-support.md) per altri dettagli.


<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>Progettazione per il partizionamento
Per ridimensionare in modo efficace con Azure Cosmos DB è necessario selezionare una chiave di partizione valida quando si crea il contenitore. È necessario considerare due aspetti fondamentali nella scelta di una chiave di partizione:

* **Limite per query e transazioni**: la scelta della chiave di partizione deve bilanciare la necessità di consentire l'uso di transazioni rispetto al requisito di distribuire le entità tra più chiavi di partizione per garantire una soluzione scalabile. Da una parte è possibile impostare la stessa chiave di partizione per tutti gli elementi. Questa scelta potrebbe tuttavia limitare la scalabilità della soluzione. Dall'altra parte è possibile assegnare a ogni elemento una chiave di partizione univoca. In questo modo, la soluzione risulterebbe altamente scalabile, ma impedirebbe di usare transazioni tra documenti diversi tramite stored procedure e trigger. Una chiave di partizione ideale consente di usare query efficienti e dispone di una quantità sufficiente di cardinalità per garantire la scalabilità della soluzione. 
* **Evitare i colli di bottiglia in termini di archiviazione e prestazioni**: è importante scegliere una proprietà che consenta di distribuire le scritture su una serie di valori distinti. Le richieste per la stessa chiave di partizione non possono superare la velocità effettiva di una partizione singola e vengono limitate. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" all'interno dell'applicazione. Poiché tutti i dati per una singola chiave di partizione devono essere archiviati in una partizione, è anche consigliabile evitare le chiavi di partizione con volumi elevati di dati per lo stesso valore. 

Verranno ora esaminati alcuni scenari reali con le chiavi di partizione corrette per ognuno:
* Se si implementa un back-end del profilo utente, l'ID utente rappresenta la scelta ideale per la chiave di partizione.
* Se si archiviano dati IoT, ad esempio lo stato del dispositivo, l'ID dispositivo rappresenta la scelta ideale per la chiave di partizione.
* Se si usa DocumentDB per la registrazione di dati di serie temporali, il nome host o l'ID processo rappresenta la scelta ottimale per la chiave di partizione.
* Se si dispone di un'architettura multi-tenant, l'ID tenant rappresenta la scelta ideale per la chiave di partizione.

In alcuni casi d'uso, come l'IoT e i profili utente, la chiave di partizione può corrispondere all'ID (chiave del documento). In altri casi, come ad esempio i dati di serie temporali, la chiave di partizione potrebbe essere diversa rispetto all'id.

### <a name="partitioning-and-loggingtime-series-data"></a>Partizionamento e registrazione di dati di serie temporali
Uno dei casi d'uso più comuni di Cosmos DB è rappresentato da registrazione e telemetria. È importante scegliere una chiave di partizione efficace perché potrebbe essere necessario scrivere/leggere grandi volumi di dati. La scelta dipende dalla frequenza di lettura e scrittura e dai tipi di query che si prevede di eseguire. Di seguito sono riportati alcuni suggerimenti su come scegliere una chiave di partizione efficace.

* Se il caso d'uso prevede una frequenza ridotta di scritture eseguite in un lungo intervallo di tempo e la necessità di eseguire query in base agli intervalli di timestamp e ad altri filtri, si consiglia l'uso di un rollup del timestamp, ad esempio l'uso della data come chiave di partizione. Ciò consente di eseguire query su tutti i dati per una data da una singola partizione. 
* Se il carico di lavoro prevede molte scritture, scenario più comune, è opportuno usare una chiave di partizione non basata su timestamp in modo che Cosmos DB possa distribuire in modo uniforme le scritture in più partizioni. In questo caso, un nome host, un ID processo, un ID attività o un'altra proprietà con una cardinalità elevata è una scelta efficace. 
* Il terzo è un approccio ibrido in cui si hanno più contenitori, uno per ogni giorno/mese, e la chiave di partizione è una proprietà granulare, ad esempio un nome host. Il vantaggio di questo approccio riguarda la possibilità di impostare diverse velocità effettive in base alla finestra temporale, ad esempio il provisioning del contenitore per il mese corrente viene eseguito con una velocità effettiva maggiore perché viene usata per letture e scritture, mentre per i mesi precedenti è possibile ridurre la velocità effettiva perché vengono usati solo per le letture.

### <a name="partitioning-and-multi-tenancy"></a>Partizionamento e multi-tenancy
Se si implementa un'applicazione multi-tenant usando Cosmos DB, sono disponibili due modelli comuni: una chiave di partizione per ogni tenant e un contenitore per ogni tenant. Di seguito sono riportati vantaggi e svantaggi di ogni modello:

* Una chiave di partizione per ogni tenant: in questo modello, i tenant vengono collocati all'interno di un singolo contenitore. Le query e gli inserimenti di elementi all'interno di un tenant possono essere tuttavia eseguiti a fronte di una partizione singola. È anche possibile implementare la logica transazionale su tutti gli elementi all'interno di un tenant. Poiché più tenant condividono un contenitore, è possibile risparmiare i costi di archiviazione e velocità effettiva raggruppando le risorse per i tenant all'interno di un singolo contenitore invece di eseguire il provisioning di capacità aggiuntiva per ogni tenant. Lo svantaggio è che non si ottiene l'isolamento delle prestazioni per ogni tenant. Gli aumenti di prestazioni/velocità effettiva si applicano all'intero contenitore rispetto agli aumenti previsti per i tenant.
* Un contenitore per ogni tenant: ogni tenant ha un contenitore proprio. In questo modello è possibile riservare le prestazioni per ogni tenant. Con il nuovo schema tariffario per il provisioning di Cosmos DB, questo modello è più conveniente per applicazioni multi-tenant con un numero ridotto di tenant.

È anche possibile usare un approccio combinato/a livelli che colloca tenant di dimensioni ridotte ed esegue la migrazione di tenant di dimensioni maggiori al relativo contenitore.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata illustrata una panoramica di concetti e procedure consigliate per il partizionamento con qualsiasi API di Azure Cosmos DB. 

* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md)
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md)




