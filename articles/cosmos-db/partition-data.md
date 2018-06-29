---
title: Partizionamento e aumento del numero di istanze in Azure Cosmos DB | Microsoft Docs
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, sulla configurazione del partizionamento e delle chiavi di partizione e su come scegliere la chiave di partizione corretta per l'applicazione.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 046e45978d401e05d0ab8154aff994052f5d7717
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960373"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partizionamento e ridimensionamento in Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è un servizio di database multimodello distribuito a livello globale, progettato per favorire prestazioni rapide e prevedibili. È ridimensionabile contestualmente all'espansione dell'applicazione. Questo articolo offre una panoramica del funzionamento del partizionamento per tutti i modelli di dati in Azure Cosmos DB. Descrive anche come configurare contenitori Azure Cosmos DB per ridimensionare in modo efficace le applicazioni.

Il partizionamento e le chiavi di partizione vengono descritti in questo video:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB
In Azure Cosmos DB è possibile archiviare dati ed eseguire query su dati senza schema con latenza nell'ordine di millisecondi a una cifra, a qualsiasi livello di scalabilità. Azure Cosmos DB offre contenitori per l'archiviazione di dati denominati *raccolte* (per i documenti), *grafici* o *tabelle*. 

I contenitori sono risorse logiche e possono comprendere una o più partizioni fisiche o server. Il numero di partizioni è determinato da Azure Cosmos DB in base allo spazio di archiviazione e alla velocità effettiva con provisioning di un contenitore o di un set di contenitori. 

Una partizione *fisica* è una quantità fissa di risorsa di archiviazione con backup su disco SSD riservato combinata con una quantità variabile di risorse di calcolo (CPU e memoria). Ogni partizione fisica viene replicata per assicurare disponibilità elevata. Ogni set di contenitori può condividere una o più partizioni fisiche. Le partizioni fisiche vengono completamente gestite da Azure Cosmos DB, quindi non è necessario scrivere codice complesso o occuparsi personalmente della gestione. I contenitori di Azure Cosmos DB sono illimitati in termini di risorse di archiviazione e di velocità effettiva. 

Una partizione *logica* è una partizione all'interno di una partizione fisica in cui sono archiviati tutti i dati associati al valore di una singola chiave di partizione. Più partizioni logiche possono essere incluse nella stessa partizione fisica. Nel diagramma seguente un singolo contenitore ha tre partizioni logiche. Ogni partizione logica archivia i dati di una chiave di partizione, rispettivamente LAX, AMS e MEL. Ognuna delle partizioni logiche LAX, AMS e MEL non può superare il limite massimo di 10 GB per le partizioni logiche. 

![Partizionamento delle risorse](./media/introduction/azure-cosmos-db-partitioning.png) 

Quando un contenitore soddisfa i [prerequisiti di partizionamento](#prerequisites), il partizionamento è completamente trasparente all'applicazione. Azure Cosmos DB supporta letture e scritture veloci, query, logica transazionale, livelli di coerenza e controllo di accesso con granularità fine tramite metodi/API per una singola risorsa di contenitore. Il servizio gestisce la distribuzione dei dati tra le partizioni fisiche e logiche e il routing delle richieste di query alla partizione corretta. 

## <a name="how-does-partitioning-work"></a>Funzionamento del partizionamento

Per il partizionamento, ogni elemento deve avere una *chiave di partizione* e una *chiave di riga* che lo identificano in modo univoco. La chiave di partizione funge da partizione logica per i dati e fornisce ad Azure Cosmos DB un limite naturale per la distribuzione dei dati tra le partizioni fisiche. I dati di una singola partizione logica devono essere contenuti all'interno di un'unica partizione fisica, ma la gestione delle partizioni fisiche viene eseguita da Azure Cosmos DB. 

In sintesi, il partizionamento in Azure Cosmos DB funziona nel modo seguente:

* Il provisioning di un set di contenitori Azure Cosmos DB viene effettuato con velocità effettiva di **T** UR/s (richieste al secondo).
* In background Azure Cosmos DB effettua il provisioning delle partizioni fisiche necessarie per gestire **T** richieste al secondo. Se **T** è maggiore della velocità effettiva massima per partizione fisica **t**, Azure Cosmos DB effettua il provisioning di **N = T/t** partizioni fisiche. Il valore della velocità effettiva massima per partizione è configurato da Azure Cosmos DB; questo valore viene assegnato in base alla velocità effettiva totale sottoposta a provisioning e alla configurazione hardware usata. 
* Azure Cosmos DB alloca lo spazio degli hash delle chiavi di partizione in modo uniforme tra le **N** partizioni fisiche. Pertanto, il numero di partizioni logiche che ogni partizione fisica ospita corrisponde a **1/N** * numero di valori di chiave di partizione.
* Quando una partizione fisica **p** raggiunge il limite di archiviazione, Azure Cosmos DB suddivide **p** in due nuove partizioni fisiche, **p1** e **p2**. Distribuisce i valori corrispondenti a circa la metà delle chiavi a ognuna delle nuove partizioni fisiche. Questa operazione di suddivisione è invisibile all'applicazione. Se una partizione fisica raggiunge il limite di archiviazione e tutti i dati della partizione fisica appartengono alla stessa chiave di partizione logica, l'operazione di suddivisione non viene eseguita. Il motivo è che tutti i dati di una singola chiave di partizione logica devono trovarsi nella stessa partizione fisica. In questo caso dovrebbe essere adottata una strategia diversa per la chiave di partizione.
* Quando si esegue il provisioning di una velocità effettiva superiore a **t*N**, Azure Cosmos DB suddivide una o più partizioni fisiche per supportare la velocità effettiva maggiore.

La semantica delle chiavi di partizione è leggermente diversa in modo da corrispondere alla semantica di ogni API, come illustrato nella tabella seguente:

| API | Chiave di partizione | Chiave di riga |
| --- | --- | --- |
| SQL | Percorso personalizzato della chiave di partizione | `id` fisso | 
| MongoDB | Chiave di partizione personalizzata  | `_id` fisso | 
| Gremlin | Proprietà chiave di partizione personalizzata | `id` fisso | 
| Tabella | `PartitionKey` fisso | `RowKey` fisso | 

Azure Cosmos DB usa il partizionamento basato su hash. Quando si scrive un elemento, Azure Cosmos DB esegue l'hashing del valore della chiave di partizione e usa il risultato con hash per determinare la partizione in cui archiviare l'elemento. Azure Cosmos DB archivia tutti gli elementi con la stessa chiave di partizione nella stessa partizione fisica. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Procedure consigliate per scegliere una chiave di partizione

La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. Scegliere un nome proprietà che contenga un'ampia gamma di valori e abbia anche modelli di accesso. Una procedura consigliata consiste nello scegliere una chiave di partizione con un numero elevato di valori distinti, ad esempio diverse centinaia o migliaia. In questo modo, è possibile distribuire il carico di lavoro in modo uniforme tra questi valori. Una chiave di partizione ideale appare spesso come filtro nelle query e ha una cardinalità sufficiente per garantire la scalabilità della soluzione.

Se una partizione fisica raggiunge il limite di archiviazione e i dati nella partizione hanno la stessa chiave di partizione, Azure Cosmos DB restituisce il messaggio *"Partition key reached maximum size of 10 GB"* (La chiave di partizione ha raggiunto la dimensione massima di 10 GB) e la partizione non viene suddivisa. La scelta di una buona chiave di partizione è una decisione molto importante. Le partizioni fisiche sono un concetto interno di Azure Cosmos DB e sono temporanee. Con Azure Cosmos DB il numero di partizioni fisiche viene scalato automaticamente in base al carico di lavoro. Pertanto è consigliabile non correlare la progettazione del database al numero di partizioni fisiche mentre bisogna accertarsi di scegliere la chiave di partizione corretta (partizioni logiche). 

Scegliere una chiave di partizione in modo che:

* La distribuzione di archiviazione sia uniforme su tutte le chiavi.
* La distribuzione del volume di richieste in un determinato punto nel tempo sia uniforme su tutte le chiavi.
* Le query che vengono richiamate con concorrenza elevata possano essere indirizzate in modo efficiente includendo la chiave di partizione nel predicato del filtro.  
* La scelta di una chiave di partizione con cardinalità più elevata è in genere preferibile perché solitamente produce distribuzione e scalabilità migliori. Ad esempio, una chiave composta può essere formata dalla concatenazione dei valori di più proprietà per aumentare la cardinalità. 

Quando si sceglie una chiave di partizione tenendo conto delle considerazioni precedenti, non è necessario preoccuparsi del numero di partizioni né della velocità effettiva allocata a ogni partizione fisica in quanto Azure Cosmos DB ridimensiona il numero di partizioni fisiche e può anche scalare le singole partizioni, se necessario.

I contenitori di Azure Cosmos DB possono essere creati come *fissi* o *senza limitazioni* nel portale di Azure. I contenitori a dimensione fissa hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR/s. Per creare un contenitore illimitato, è necessario specificare una chiave di partizione e una velocità effettiva minima di 1.000 UR/s. I contenitori di Azure DB Cosmos possono anche essere configurati per condividere la velocità effettiva tra un set di contenitori, in cui ogni contenitore deve specificare una chiave partizione e si può espandere senza limiti.

È consigliabile controllare il modo in cui i dati vengono distribuiti tra le partizioni. Per controllare la distribuzione dei dati nel portale, accedere al proprio account Azure Cosmos DB e fare clic su **Metriche** nella sezione **Monitoraggio** e quindi fare clic sulla scheda **Archiviazione** per verificare il modo in cui i dati sono partizionati tra partizioni fisiche diverse.

![Partizionamento delle risorse](./media/partition-data/partitionkey-example.png)

L'immagine a sinistra mostra il risultato di una chiave di partizione non valida, mentre l'immagine a destra mostra il risultato di una chiave di partizione valida. Nell'immagine a sinistra è possibile osservare che i dati non sono distribuiti uniformemente tra le partizioni. È consigliabile scegliere una chiave di partizione che distribuisca i dati in modo simile all'immagine a destra.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Prerequisiti per il partizionamento

Per consentire la suddivisione automatica delle partizioni fisiche in **p1** e **p2**, come descritto nella sezione [Funzionamento del partizionamento](#how-does-partitioning-work), è necessario creare il contenitore con una velocità effettiva di almeno 1.000 UR/s (o condividere la velocità effettiva tra un set di contenitori) e specificare una chiave di partizione. Durante la creazione di un contenitore, ad esempio una raccolta, un grafo o una tabella, nel portale di Azure, selezionare l'opzione di capacità di archiviazione **Senza limitazioni** per sfruttare i vantaggi della scalabilità illimitata. 

Se è stato creato un contenitore nel portale di Azure o a livello di codice con velocità effettiva iniziale uguale o maggiore di 1.000 UR/s ed è stata specificata una chiave di partizione, è possibile sfruttare la scalabilità illimitata senza apportare alcuna modifica al contenitore. Questo riguarda anche i contenitori con opzione di capacità **Fissa**, purché il contenitore iniziale sia stato creato con velocità effettiva di almeno 1.000 UR/s e sia stata specificata una chiave di partizione.

Tutti i contenitori configurati per condividere la velocità effettiva come parte di un set di contenitori vengono considerati contenitori **senza limiti**.

Se è stato creato un contenitore con opzione di capacità **Fissa** senza chiave di partizione o con velocità effettiva minore di 1.000 UR/s, il contenitore non si ridimensionerà automaticamente come descritto in questo articolo. Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore senza limiti, ad esempio uno con velocità effettiva di almeno 1.000 UR/s e una chiave di partizione, è necessario usare l'[Utilità di migrazione dati](import-data.md) o la [Libreria di feed di modifiche](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Partizionamento e velocità effettiva con provisioning
Azure Cosmos DB è progettato per prestazioni prevedibili. Quando si crea un contenitore o un set di contenitori, la velocità effettiva viene riservata in termini di *[Unità richiesta](request-units.md) (UR) al secondo*. A ogni richiesta viene assegnato un addebito di UR proporzionale alla quantità di risorse di sistema, come CPU, memoria e I/O utilizzati dall'operazione. La lettura di un documento di 1 KB con coerenza di sessione usa 1 UR. Un'operazione di lettura corrisponde a 1 RU indipendentemente dal numero di elementi archiviati o dal numero di richieste simultanee in esecuzione contemporaneamente. Elementi di dimensioni maggiori richiedono più UR a seconda delle dimensioni. Se si conoscono le dimensioni delle entità e il numero di letture che è necessario supportare per l'applicazione, è possibile effettuare il provisioning della quantità esatta di velocità effettiva necessaria per le esigenze dell'applicazione. 

> [!NOTE]
> Per usare completamente la velocità effettiva con provisioning di un contenitore o di un set di contenitori, è necessario scegliere una chiave di partizione che permetta di distribuire in modo uniforme le richieste tra tutti i valori di chiave di partizione distinti.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Uso delle API di Azure Cosmos DB
È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per creare contenitori e ridimensionarli in qualsiasi momento. Questa sezione mostra come creare contenitori e specificare la velocità effettiva e la chiave di partizione assegnate tramite provisioning usando ogni API.


### <a name="sql-api"></a>API SQL
L'esempio seguente mostra come creare un contenitore (raccolta) usando l'API SQL. 

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

Per altre informazioni, vedere [Partizionamento in Azure Cosmos DB con l'API SQL](sql-api-partition-data.md).

### <a name="mongodb-api"></a>API di MongoDB
Con l'API MongoDB è possibile creare una raccolta partizionata usando il proprio strumento, driver o SDK preferito. In questo esempio verrà usato Mongo Shell per creare una raccolta.

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

Per creare una tabella usando l'API di tabella, usare il metodo `CreateIfNotExists`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

La velocità effettiva assegnata tramite provisioning viene impostata come argomento di `CreateIfNotExists`. La chiave di partizione viene creata implicitamente come valore `PartitionKey`. 

È possibile recuperare una singola entità usando il codice seguente:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Per altre informazioni, vedere [Sviluppare con l'API Table](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>API Gremlin

Con l'API Gremlin, è possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per creare un contenitore che rappresenta un grafo. In alternativa, poiché Azure Cosmos DB è multimodello, per creare e ridimensionare il contenitore grafo è possibile usare una delle altre API.

È possibile leggere qualsiasi vertice o arco usando la chiave di partizione e l'ID in Gremlin. Per un grafico con area ("USA") come chiave di partizione e "Seattle" come chiave di riga, ad esempio, è possibile trovare un vertice usando la sintassi seguente:

```
g.V(['USA', 'Seattle'])
```

È possibile fare riferimento a un arco usando la chiave di partizione e la chiave di riga.

```
g.E(['USA', 'I5'])
```

Per altre informazioni, vedere [Uso di un grafo partizionato in Azure Cosmos DB](graph-partitioning.md).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Progettare per la scalabilità
Per ridimensionare in modo efficace con Azure Cosmos DB è necessario selezionare una chiave di partizione valida quando si crea il contenitore. Nello scegliere una chiave di partizione è necessario considerare due aspetti principali:

* **Limite delle query e transazioni**. La scelta della chiave di partizione deve bilanciare l'esigenza di poter usare transazioni con il requisito di distribuire le entità tra più chiavi di partizione per garantire una soluzione scalabile. Da una parte è possibile impostare la stessa chiave di partizione per tutti gli elementi. Questa opzione potrebbe comunque limitare la scalabilità della soluzione. Dall'altra è possibile assegnare una chiave di partizione univoca per ogni elemento. Questa scelta è altamente scalabile, ma impedisce di usare transazioni tra documenti tramite stored procedure e trigger. Una chiave di partizione ideale consente di usare query efficienti e dispone di una quantità di cardinalità idonea a garantire la scalabilità della soluzione. 
* **Evitare i colli di bottiglia in termini di archiviazione e prestazioni**. È importante scegliere una proprietà che consenta di distribuire le scritture su una serie di valori distinti. Le richieste alla stessa chiave di partizione non possono superare la velocità effettiva allocata tramite provisioning a una partizione e avranno una frequenza limitata. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" all'interno dell'applicazione. Poiché tutti i dati per una singola chiave di partizione devono essere archiviati in una partizione, è consigliabile evitare le chiavi di partizione con volumi elevati di dati per lo stesso valore. 

Verranno ora esaminati alcuni scenari reali con le chiavi di partizione corrette per ognuno:
* Se si implementa un back-end del profilo utente, l'*ID utente* rappresenta la scelta ideale per una chiave di partizione.
* Se si archiviano dati IoT, ad esempio lo stato del dispositivo, un *ID dispositivo* rappresenta la scelta ideale per una chiave di partizione.
* Se si usa Azure Cosmos DB per la registrazione di dati di serie temporali, il *nome host* o l'*ID processo* rappresenta la scelta ottimale per una chiave di partizione.
* In presenza di un'architettura multi-tenant, l'*ID tenant* rappresenta la scelta ideale per una chiave di partizione.

In alcuni casi d'uso, ad esempio con IoT e i profili utente, la chiave di partizione può corrispondere al proprio *ID* (chiave del documento). In altri casi, ad esempio i dati di serie temporali, la chiave di partizione potrebbe essere diversa dall'*ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Partizionamento e registrazione di dati di serie temporali
Uno dei casi d'uso più comuni in Azure Cosmos DB è costituito da registrazione e telemetria. In questo scenario è importante scegliere una chiave di partizione appropriata, perché potrebbe essere necessario scrivere/leggere grandi volumi di dati. La scelta della chiave di partizione dipende dalla frequenza di lettura e scrittura e dai tipi di query che si prevede di eseguire. Di seguito sono riportati alcuni suggerimenti su come scegliere una chiave di partizione efficace:

* Se il caso d'uso prevede frequenze di scrittura ridotte che si accumulano in un lungo periodo di tempo ed è necessario eseguire query per intervalli di timestamp e altri filtri, usare un'implementazione del timestamp. Ad esempio, un approccio valido consiste nell'usare la data come chiave di partizione. Con questo approccio è possibile eseguire query su tutti i dati per una data specifica da una singola partizione. 
* Se il carico di lavoro è a elevato utilizzo di scrittura, una caratteristica molto comune in questo scenario, usare una chiave di partizione non basata sul timestamp. In questo modo, Azure Cosmos DB può distribuire e ridimensionare uniformemente le scritture tra più partizioni. In questo caso, un *nome host*, un *ID processo*, un *ID attività* o un'altra proprietà con cardinalità elevata è una scelta efficace. 
* Un altro approccio è quello ibrido, in cui sono presenti più contenitori, uno per ogni giorno/mese, e la chiave di partizione è una proprietà più granulare, ad esempio un *nome host*. Questo approccio ha il vantaggio di poter impostare una velocità effettiva diversa per ogni contenitore o set di contenitori in base all'intervallo di tempo e alle esigenze di scalabilità e prestazioni. Ad esempio, un contenitore per il mese in corso può essere sottoposto a provisioning con una velocità effettiva superiore, poiché gestisce letture e scritture. Per i mesi precedenti è possibile effettuare il provisioning di una velocità effettiva inferiore, perché vengono gestite solo letture.

### <a name="partitioning-and-multitenancy"></a>Partizionamento e multi-tenancy
Se si implementa un'applicazione multi-tenant usando Azure Cosmos DB, è possibile prendere in considerazione due scenari comuni: *una chiave di partizione per ogni tenant* e *un contenitore per ogni tenant*. Di seguito sono riportati vantaggi e svantaggi di ogni modello:

* **Una chiave di partizione per ogni tenant**. In questo modello i tenant vengono collocati all'interno di un singolo contenitore. Le query e gli inserimenti per un singolo tenant possono essere eseguiti su un'unica partizione. È anche possibile implementare la logica transazionale su tutti gli elementi che appartengono a un tenant. Poiché più tenant condividono un contenitore, è possibile utilizzare al meglio l'archiviazione e la velocità effettiva assegnata tramite provisioning raggruppando le risorse per tutti i tenant all'interno di un singolo contenitore invece di effettuare il provisioning per ogni tenant. Lo svantaggio è che non si ottiene l'isolamento delle prestazioni per ogni tenant. L'aumento della velocità effettiva per garantire le prestazioni verrà applicato all'intero contenitore con tutti i tenant, anziché eseguire un aumento mirato per un singolo tenant.
* **Un contenitore per ogni tenant**. In questo modello ogni tenant ha il proprio contenitore ed è possibile riservare velocità effettiva con prestazioni garantite per ogni tenant. Questo modello è più conveniente per applicazioni multi-tenant con un numero ridotto di tenant.

È anche possibile usare un approccio ibrido che colloca tenant di dimensioni ridotte e isola i tenant di dimensioni maggiori nei rispettivi contenitori.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata presentata una panoramica di concetti e procedure consigliate per la scalabilità e il partizionamento in Azure Cosmos DB. 

* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).



