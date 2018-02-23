---
title: Partizionamento e aumento del numero di istanze in Azure Cosmos DB | Microsoft Docs
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, sulla configurazione del partizionamento e delle chiavi di partizione e su come scegliere la chiave di partizione corretta per l'applicazione.
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
ms.date: 01/05/2018
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0032a00883cedfe754e14293dc13a1009f6dd3a0
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partizionamento e ridimensionamento in Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è un servizio di database multimodello distribuito a livello globale che consente di ottenere prestazioni rapide e prevedibile. È ridimensionabile contestualmente all'espansione dell'applicazione. Questo articolo offre una panoramica del funzionamento del partizionamento per tutti i modelli di dati in Azure Cosmos DB. Descrive anche come configurare contenitori Azure Cosmos DB per ridimensionare in modo efficace le applicazioni.

Il partizionamento e le chiavi di partizione sono illustrati in questo video di Azure Friday con Scott Hanselman e Shireesh Thota, responsabile principale della progettazione di Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB
In Azure Cosmos DB è possibile archiviare dati ed eseguire query senza schema con tempi di risposta nell'ordine di millisecondi su qualsiasi scala. Azure Cosmos DB offre contenitori per l'archiviazione di dati denominati *raccolte* (per i documenti), *grafici* o *tabelle*. 

I contenitori sono risorse logiche e possono comprendere una o più partizioni fisiche o server. Il numero di partizioni è determinato da Azure Cosmos DB in base allo spazio di archiviazione e alla velocità effettiva con provisioning del contenitore. 

Una partizione fisica è una quantità fissa di spazio di archiviazione riservato basato su SSD. Ogni partizione fisica viene replicata per assicurare disponibilità elevata. Una o più partizioni fisiche costituiscono un contenitore. Le partizioni fisiche vengono completamente gestite da Azure Cosmos DB, quindi non è necessario scrivere codice complesso o occuparsi personalmente della gestione. I contenitori di Azure Cosmos DB sono illimitati in termini di risorse di archiviazione e di velocità effettiva. 

Una partizione logica è una partizione all'interno di una partizione fisica che archivia tutti i dati associati a un valore di una singola chiave di partizione. Una partizione logica ha una capacità massima di 10 GB. Nel diagramma seguente è raffigurato un singolo contenitore con tre partizioni logiche. Ogni partizione logica archivia i dati di una chiave di partizione, rispettivamente LAX, AMS e MEL. Ognuna delle partizioni logiche LAX, AMS e MEL non può superare il limite massimo di 10 GB per le partizioni logiche. 

![Partizionamento delle risorse](./media/introduction/azure-cosmos-db-partitioning.png) 

Quando una raccolta soddisfa i [prerequisiti di partizionamento](#prerequisites), il partizionamento stesso è un'operazione trasparente all'applicazione. Azure Cosmos DB supporta letture e scritture veloci, query, logica transazionale, livelli di coerenza e controllo di accesso con granularità fine tramite metodi/API per una singola risorsa di contenitore. Il servizio gestisce la distribuzione dei dati tra le partizioni fisiche e logiche e il routing delle richieste di query alla partizione corretta. 

## <a name="how-does-partitioning-work"></a>Funzionamento del partizionamento

Per il partizionamento, ogni elemento deve avere una chiave di partizione e una chiave di riga che lo identificano in modo univoco. La chiave di partizione funge da partizione logica per i dati e fornisce ad Azure Cosmos DB un limite naturale per la distribuzione dei dati tra le partizioni fisiche. Tenere presente che i dati di una singola partizione logica devono essere contenuti in un'unica partizione fisica, ma la gestione delle partizioni fisiche viene eseguita da Azure Cosmos DB. 

In sintesi, il partizionamento in Azure Cosmos DB funziona nel modo seguente:

* Si esegue il provisioning di un contenitore Azure Cosmos DB con velocità effettiva di **T** richieste al secondo.
* In background Azure Cosmos DB effettua il provisioning delle partizioni necessarie per gestire **T** richieste al secondo. Se **T** è maggiore della velocità effettiva massima per partizione **t**, Azure Cosmos DB effettua il provisioning di **N = T/t** partizioni.
* Azure Cosmos DB alloca lo spazio degli hash delle chiavi di partizione in modo uniforme tra le **N** partizioni. Ogni partizione fisica ospita quindi **1/N** valori di chiave di partizione (partizioni logiche).
* Quando una partizione fisica **p** raggiunge il limite di archiviazione, Azure Cosmos DB suddivide **p** in due nuove partizioni, **p1** e **p2**. Distribuisce i valori corrispondenti a circa la metà delle chiavi a ogni partizione. Questa operazione di suddivisione è invisibile all'applicazione. Se una partizione fisica raggiunge il limite di archiviazione e tutti i dati della partizione fisica appartengono alla stessa chiave di partizione logica, l'operazione di suddivisione non viene eseguita. Tutti i dati di una singola chiave di partizione logica, infatti, devono trovarsi nella stessa partizione fisica, pertanto la partizione fisica non può essere suddivisa in p1 e p2. In questo caso dovrebbe essere adottata una strategia diversa per la chiave di partizione.
* Quando si esegue il provisioning di una velocità effettiva superiore a **t*N**, Azure Cosmos DB suddivide una o più partizioni per supportare la velocità effettiva maggiore.

La semantica delle chiavi di partizione è leggermente diversa in modo da corrispondere alla semantica di ogni API, come illustrato nella tabella seguente:

| API | Chiave di partizione | Chiave di riga |
| --- | --- | --- |
| Azure Cosmos DB | Percorso personalizzato della chiave di partizione | `id` fisso | 
| MongoDB | Chiave personalizzata condivisa  | `_id` fisso | 
| Grafico | Proprietà chiave di partizione personalizzata | `id` fisso | 
| Tabella | `PartitionKey` fisso | `RowKey` fisso | 

Azure Cosmos DB usa il partizionamento basato su hash. Quando si scrive un elemento, Azure Cosmos DB esegue l'hashing del valore della chiave di partizione e usa il risultato con hash per determinare la partizione in cui archiviare l'elemento. Azure Cosmos DB archivia tutti gli elementi con la stessa chiave di partizione nella stessa partizione fisica. La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. È necessario scegliere un nome proprietà che contenga un'ampia gamma di valori e abbia anche modelli di accesso. Se una partizione fisica raggiunge il limite di archiviazione e tutti i dati della partizione appartengono alla stessa chiave di partizione, Azure Cosmos DB restituisce un errore che informa che la chiave di partizione ha raggiunto le dimensioni massime di 10 GB e la partizione non viene divisa, pertanto la scelta della chiave di partizione è una decisione molto importante.

> [!NOTE]
> È consigliabile avere una chiave di partizione con molti valori distinti (centinaia di migliaia come minimo).
>

I contenitori di Azure Cosmos DB possono essere creati come *fissi* o *senza limitazioni* nel portale di Azure. I contenitori a dimensione fissa hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR/s. Per creare un contenitore senza limitazioni, è necessario specificare una velocità effettiva minima di 1.000 UR/s e una chiave di partizione.

È consigliabile controllare la modalità di distribuzione dei dati nelle partizioni. Per eseguire il controllo nel portale, accedere al proprio account Azure Cosmos DB e fare clic su **Metrics** (Metriche) nella sezione **Monitoring** (Monitoraggio) e quindi nel riquadro destro fare clic sulla scheda **storage** (Archiviazione) per visualizzare come sono partizionati i dati nelle diverse partizioni fisiche.

![Partizionamento delle risorse](./media/partition-data/partitionkey-example.png)

L'immagine a sinistra mostra il risultato di una chiave di partizione non valida l'immagine a destra mostra il risultato di una chiave di partizione valida. Nell'immagine a sinistra è possibile vedere che i dati non sono distribuiti uniformemente tra le partizioni. È consigliabile distribuire i dati in modo che il grafico sia simile all'immagine di destra.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Prerequisiti per il partizionamento

Per consentire la suddivisione automatica delle partizioni fisiche in **p1** e **p2**, come descritto nella sezione [Funzionamento del partizionamento](#how-does-partitioning-work), è necessario creare il contenitore con una velocità effettiva di almeno 1.000 UR/s e specificare una chiave di partizione. Durante la creazione di un contenitore nel portale di Azure selezionare l'opzione di capacità di archiviazione **Senza limitazioni** per sfruttare i vantaggi del partizionamento e del ridimensionamento automatico. 

Se si è creato un contenitore nel portale di Azure o a livello di programmazione con una velocità effettiva iniziale di almeno 1.000 UR/s e se i dati includono una chiave di partizione, è possibile sfruttare i vantaggi del partizionamento senza modificare il contenitore, inclusi i contenitori con dimensioni **fisse**, purché il contenitore iniziale sia stato creato con una velocità effettiva di almeno 1.000 UR/s e una chiave di partizione sia presente nei dati.

Se si è creato un contenitore di dimensioni **fisse** senza chiave di partizione o un contenitore di dimensioni **fisse** con una velocità effettiva inferiore a 1.000 UR/s, il contenitore non può essere suddiviso automaticamente come descritto in questo articolo. Per eseguire la migrazione dei dati da un contenitore di questo tipo a un contenitore senza limitazioni, ossia con una velocità effettiva di almeno 1.000 UR/s e una chiave di partizione, è necessario usare l'[Utilità di migrazione dati](import-data.md) o la [libreria di feed di modifiche](change-feed.md) per eseguire la migrazione delle modifiche. 

## <a name="partitioning-and-provisioned-throughput"></a>Partizionamento e velocità effettiva con provisioning
Azure Cosmos DB è progettato per prestazioni prevedibili. Quando si crea un contenitore, la velocità effettiva viene riservata in termini di *[unità richiesta](request-units.md) (UR) al secondo*. A ogni richiesta viene assegnato un addebito delle UR proporzionato alla quantità di risorse di sistema, come CPU, memoria e I/O usati dall'operazione. La lettura di un documento di 1 KB con coerenza di sessione usa 1 UR. Un'operazione di lettura corrisponde a 1 RU indipendentemente dal numero di elementi archiviati o dal numero di richieste simultanee in esecuzione contemporaneamente. Elementi di dimensioni maggiori richiedono più UR a seconda delle dimensioni. Se si conoscono le dimensioni delle entità e il numero di letture che è necessario supportare per l'applicazione, è possibile eseguire il provisioning della quantità esatta di velocità effettiva necessaria per le esigenze di lettura dell'applicazione. 

> [!NOTE]
> Per ottenere la velocità effettiva totale del contenitore è necessario scegliere una chiave di partizione che consenta di distribuire in modo uniforme le richieste tra alcuni valori distinti della chiave di partizione.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Uso delle API di Azure Cosmos DB
È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per creare contenitori e ridimensionarli in qualsiasi momento. Questa sezione illustra come creare contenitori e specificare la definizione di velocità effettiva e chiave di partizione in ognuna delle API supportate.

### <a name="azure-cosmos-db-api"></a>API di Azure Cosmos DB
L'esempio seguente illustra come creare un contenitore (raccolta) usando l'API di Azure Cosmos DB. 

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

È possibile leggere un elemento (documento) tramite il metodo `GET` nell'API REST oppure usando `ReadDocumentAsync` in un SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>API di MongoDB
Con l'API MongoDB è possibile creare una raccolta partizionata usando il proprio strumento, driver o SDK preferito. In questo esempio verrà usato Mongo Shell per la creazione della raccolta.

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

Per creare una tabella mediante l'API Tabelle di Azure Cosmos DB, usare il metodo CreateIfNotExists. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

La velocità effettiva viene impostata come argomento di CreateIfNotExists.

La chiave di partizione viene creata implicitamente come valore `PartitionKey`. 

È possibile recuperare una singola entità tramite il frammento seguente:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Per altre informazioni, vedere [Sviluppare con l'API Table](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>API Graph

Con l'API Graph è necessario usare il portale o l'interfaccia della riga di comando di Azure per creare contenitori. Essendo Azure Cosmos DB multimodello, in alternativa è possibile usare un altro modello per creare e ridimensionare il contenitore di grafici.

È possibile leggere qualsiasi vertice o arco usando la chiave di partizione e l'ID in Gremlin. Per un grafico con area ("USA") come chiave di partizione e "Seattle" come chiave di riga, ad esempio, è possibile trovare un vertice usando la sintassi seguente:

```
g.V(['USA', 'Seattle'])
```

È possibile fare riferimento a un arco usando la chiave di partizione e la chiave di riga.

```
g.E(['USA', 'I5'])
```

Per altre informazioni, vedere [Gremlin support for Azure Cosmos DB](gremlin-support.md) (Supporto Gremlin per Azure Cosmos DB).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Progettazione del partizionamento
Per ridimensionare in modo efficace con Azure Cosmos DB è necessario selezionare una chiave di partizione valida quando si crea il contenitore. È necessario considerare due aspetti principali nella scelta di una chiave di partizione:

* **Limite per query e transazioni**. La scelta della chiave di partizione deve bilanciare la necessità di consentire l'uso di transazioni rispetto al requisito di distribuire le entità tra più chiavi di partizione per garantire una soluzione scalabile. Da una parte è possibile impostare la stessa chiave di partizione per tutti gli elementi. Questa opzione potrebbe comunque limitare la scalabilità della soluzione. Dall'altra è possibile assegnare una chiave di partizione univoca per ogni elemento. Questa scelta è altamente scalabile, ma impedisce di usare transazioni tra documenti tramite stored procedure e trigger. Una chiave di partizione ideale consente di usare query efficienti e dispone di una quantità di cardinalità idonea a garantire la scalabilità della soluzione. 
* **Evitare i colli di bottiglia in termini di archiviazione e prestazioni**. È importante scegliere una proprietà che consenta di distribuire le scritture su una serie di valori distinti. Le richieste per la stessa chiave di partizione non possono superare la velocità effettiva di una partizione singola e vengono limitate. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" all'interno dell'applicazione. Poiché tutti i dati per una singola chiave di partizione devono essere archiviati in una partizione, è consigliabile evitare le chiavi di partizione con volumi elevati di dati per lo stesso valore. 

Verranno ora esaminati alcuni scenari reali con le chiavi di partizione corrette per ognuno:
* Se si implementa un back-end del profilo utente, l'ID utente rappresenta la scelta ideale per la chiave di partizione.
* Se si archiviano dati IoT, ad esempio lo stato del dispositivo, l'ID dispositivo rappresenta la scelta ideale per la chiave di partizione.
* Se si usa Azure Cosmos DB per la registrazione di dati di serie temporali, il nome host o l'ID processo rappresenta la scelta ottimale per la chiave di partizione.
* Se si dispone di un'architettura multi-tenant, l'ID tenant rappresenta la scelta ideale per la chiave di partizione.

In alcuni casi d'uso, come l'IoT e i profili utente, la chiave di partizione può corrispondere all'ID (chiave del documento). In altri casi, come ad esempio i dati di serie temporali, la chiave di partizione potrebbe essere diversa rispetto all'ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Partizionamento e registrazione di dati di serie temporali
Uno dei casi d'uso più comuni di Azure Cosmos DB è rappresentato da registrazione e telemetria. È importante scegliere una chiave di partizione efficace perché potrebbe essere necessario scrivere/leggere grandi volumi di dati. La scelta dipende dalla frequenza di lettura e scrittura e dai tipi di query che si prevede di eseguire. Di seguito sono riportati alcuni suggerimenti su come scegliere una chiave di partizione efficace:

* Se il caso d'uso prevede frequenze di scrittura ridotte che si accumulano nel tempo ed è necessario eseguire una query per intervalli di timestamp e altri filtri, usare un'implementazione del timestamp. Ad esempio, un approccio valido consiste nell'usare la data come chiave di partizione. Con questo approccio è possibile eseguire query su tutti i dati per una data da una singola partizione. 
* Se il carico di lavoro prevede molte scritture, scenario più comune, usare una chiave di partizione non basata su timestamp. Con questo approccio Azure Cosmos DB può distribuire in modo uniforme le scritture in più partizioni. In questo caso, un nome host, un ID processo, un ID attività o un'altra proprietà con una cardinalità elevata è una scelta efficace. 
* Un altro approccio è quello ibrido, in cui si hanno più contenitori, uno per ogni giorno/mese, e la chiave di partizione è una proprietà granulare, ad esempio un nome host. Questo approccio offre il vantaggio di impostare una velocità effettiva diversa in base all'intervallo di tempo. Ad esempio, il contenitore per il mese in corso viene sottoposto a provisioning con una velocità effettiva superiore, poiché gestisce letture e scritture. I mesi precedenti vengono sottoposti a provisioning con velocità effettiva inferiore poiché gestisce solo letture.

### <a name="partitioning-and-multitenancy"></a>Partizionamento e multi-tenancy
Se si implementa un'applicazione multi-tenant usando Azure Cosmos DB, sono disponibili due criteri comuni: una chiave di partizione per ogni tenant e un contenitore per ogni tenant. Di seguito sono riportati vantaggi e svantaggi di ogni modello:

* **Una chiave di partizione per ogni tenant**. In questo modello i tenant vengono collocati all'interno di un singolo contenitore. Le query e gli inserimenti di elementi all'interno di un tenant possono essere tuttavia eseguiti a fronte di una partizione singola. È anche possibile implementare la logica transazionale su tutti gli elementi all'interno di un tenant. Poiché più tenant condividono un contenitore, è possibile risparmiare i costi di archiviazione e velocità effettiva raggruppando le risorse per i tenant all'interno di un singolo contenitore invece di eseguire il provisioning di capacità aggiuntiva per ogni tenant. Lo svantaggio è che non si ottiene l'isolamento delle prestazioni per ogni tenant. Gli aumenti di prestazioni/velocità effettiva si applicano all'intero contenitore rispetto agli aumenti previsti per i tenant.
* **Un contenitore per ogni tenant**. In questo modello ogni tenant dispone di un proprio contenitore ed è possibile riservare prestazioni per ogni tenant. Con il nuovo tariffario per il provisioning di Azure Cosmos DB, questo modello è più conveniente per applicazioni multi-tenant con un numero ridotto di tenant.

È anche possibile usare un approccio combinato/a livelli che colloca tenant di dimensioni ridotte ed esegue la migrazione di tenant di dimensioni maggiori al relativo contenitore.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata illustrata una panoramica di concetti e procedure consigliate per il partizionamento con qualsiasi API di Azure Cosmos DB. 

* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).



