---
title: Output di Analisi di flusso di Azure in Azure Cosmos DB
description: Questo articolo descrive come usare Analisi di flusso di Azure per salvare output JSON in Azure Cosmos DB, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986989"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
Analisi di flusso di Azure può avere come destinazione [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, abilitando l'archiviazione dei dati e le query a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Azure Cosmos DB, vedere la [documentazione Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) per iniziare. 

> [!Note]
> A questo punto, analisi di flusso supporta la connessione a Azure Cosmos DB solo tramite l' *API SQL*.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se si esegue il punto di analisi di flusso per Azure Cosmos DB account creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Nozioni di base di Azure Cosmos DB come destinazione di output
L'output Azure Cosmos DB in analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nei contenitori di Azure Cosmos DB. 

Analisi di flusso non crea contenitori nel database. Al contrario, è necessario crearli in primo piano. È quindi possibile controllare i costi di fatturazione dei contenitori Azure Cosmos DB. Puoi anche ottimizzare le prestazioni, la coerenza e la capacità dei tuoi contenitori direttamente usando le [api Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Le sezioni seguenti illustrano in dettaglio alcune opzioni del contenitore per Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Coerenza, disponibilità e latenza di ottimizzazione
Per soddisfare i requisiti dell'applicazione, Azure Cosmos DB consente di ottimizzare il database e i contenitori e di effettuare compromessi tra coerenza, disponibilità, latenza e velocità effettiva. 

A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. È possibile migliorare la velocità effettiva scalando le unità richiesta (UR) nel contenitore. 

Per impostazione predefinita, Azure Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nel contenitore. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura in Azure Cosmos DB. 

Per altre informazioni, vedere l'articolo [modificare i livelli di coerenza del database e delle query](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di analisi di flusso con Azure Cosmos DB consente di inserire o aggiornare i record nel contenitore in base a una determinata colonna **ID documento** . Viene anche chiamato *Upsert*.

Analisi di flusso usa un approccio Upsert ottimistico. Gli aggiornamenti si verificano solo quando un inserimento ha esito negativo con un conflitto ID documento. 

Con il livello di compatibilità 1,0, analisi di flusso esegue questo aggiornamento come operazione di PATCH, in modo da consentire aggiornamenti parziali al documento. Analisi di flusso aggiunge nuove proprietà o sostituisce in modo incrementale una proprietà esistente. Tuttavia, le modifiche apportate ai valori delle proprietà di matrice nel documento JSON comportano la sovrascrittura dell'intera matrice. Ovvero non viene eseguito il merge della matrice. 

Con 1,2, il comportamento di Upsert viene modificato per inserire o sostituire il documento. La sezione successiva sul livello di compatibilità 1,2 descrive ulteriormente questo comportamento.

Se il documento JSON in arrivo ha un campo ID esistente, tale campo viene usato automaticamente come colonna **ID documento** in Azure Cosmos DB. Tutte le scritture successive vengono gestite come tali, causando una delle situazioni seguenti:

- Gli ID univoci portano a INSERT.
- ID duplicati e **ID documento** impostati su **ID** portano a Upsert.
- ID duplicati e **ID documento** non impostati generano un errore, dopo il primo documento.

Se si desidera salvare *tutti* i documenti, inclusi quelli che hanno un ID duplicato, rinominare il campo ID nella query (usando la parola chiave **As** ). Consentire Azure Cosmos DB creare il campo ID o sostituire l'ID con il valore di un'altra colonna (usando la parola chiave **As** o l'impostazione **ID documento** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partizionamento dei dati in Azure Cosmos DB
Azure Cosmos DB ridimensiona automaticamente le partizioni in base al carico di lavoro. È quindi consigliabile usare contenitori [illimitati](../cosmos-db/partition-data.md) come approccio per il partizionamento dei dati. Quando analisi di flusso scrive in contenitori illimitati, usa il numero di writer paralleli del passaggio della query precedente o lo schema di partizionamento dell'input.

> [!NOTE]
> Analisi di flusso di Azure supporta solo contenitori illimitati con chiavi di partizione al primo livello. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate, ad esempio `/region/name`, non sono supportate. 

A seconda della scelta della chiave di partizione, è possibile che venga visualizzato il _messaggio di avviso_seguente:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

È importante scegliere una proprietà della chiave di partizione con un numero di valori distinti e che consente di distribuire il carico di lavoro in modo uniforme tra questi valori. Come artefatto naturale del partizionamento, le richieste che coinvolgono la stessa chiave di partizione sono limitate dalla velocità effettiva massima di una singola partizione. 

La dimensione di archiviazione per i documenti che appartengono alla stessa chiave di partizione è limitata a 10 GB. Una chiave di partizione ideale viene visualizzata spesso come filtro nelle query e ha una cardinalità sufficiente per garantire la scalabilità della soluzione.

Una chiave di partizione è anche il limite per le transazioni in stored procedure e trigger per Azure Cosmos DB. È necessario scegliere la chiave di partizione in modo che i documenti che si verificano insieme nelle transazioni condividano lo stesso valore della chiave di partizione. Per informazioni dettagliate sulla scelta di una chiave di partizione, in Azure Cosmos DB viene visualizzato il [partizionamento](../cosmos-db/partitioning-overview.md) degli articoli.

Per i contenitori Azure Cosmos DB fissi, l'analisi di flusso non consente di aumentare o ridurre le prestazioni dopo che sono state completate. Hanno un limite superiore di 10 GB e 10.000 UR/sec di velocità effettiva. Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore illimitato (ad esempio, uno con almeno 1.000 UR/s e una chiave di partizione), usare l' [utilità di migrazione dati](../cosmos-db/import-data.md) o la [libreria di feed delle modifiche](../cosmos-db/change-feed.md).

La possibilità di scrivere in più contenitori fissi è deprecata. Non è consigliabile per la scalabilità orizzontale del processo di analisi di flusso.

## <a name="improved-throughput-with-compatibility-level-12"></a>Velocità effettiva migliorata con il livello di compatibilità 1,2
Con il livello di compatibilità 1,2, analisi di flusso supporta l'integrazione nativa per la scrittura bulk in Azure Cosmos DB. Questa integrazione consente di scrivere in modo efficace per Azure Cosmos DB massimizzando la velocità effettiva e gestendo in modo efficiente le richieste di limitazione. 

Il meccanismo di scrittura migliorato è disponibile con un nuovo livello di compatibilità a causa della differenza nel comportamento del Upsert. Con i livelli precedenti a 1,2, il comportamento di Upsert consiste nell'inserimento o nell'Unione del documento. Con 1,2, il comportamento di Upsert viene modificato per inserire o sostituire il documento.

Con i livelli precedenti a 1,2, analisi di flusso usa una stored procedure personalizzata per eseguire la Upsert bulk dei documenti per ogni chiave di partizione in Azure Cosmos DB. Un batch viene scritto come transazione. Anche quando un singolo record raggiunge un errore temporaneo (limitazione), è necessario ritentare l'intero batch. Questo rende gli scenari con una limitazione ancora ragionevole relativamente lenta.

L'esempio seguente mostra due processi di analisi di flusso identici che leggono dallo stesso input di hub eventi di Azure. Entrambi i processi di analisi di flusso sono [completamente partizionati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) con una query passthrough e scrivono in contenitori di Azure Cosmos DB identici. Le metriche a sinistra dipendono dal processo configurato con il livello di compatibilità 1,0. Le metriche a destra sono configurate con 1,2. La chiave di partizione di un contenitore Azure Cosmos DB è un GUID univoco che deriva dall'evento di input.

![Confronto tra le metriche di analisi di flusso](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

La frequenza degli eventi in ingresso negli hub eventi è due volte superiore a quella dei contenitori Azure Cosmos DB (20.000 UR) sono configurati per l'esecuzione, quindi la limitazione è prevista in Azure Cosmos DB. Tuttavia, il processo con 1,2 sta scrivendo in modo coerente a una velocità effettiva superiore (eventi di output al minuto) e con una media inferiore del tempo di utilizzo. Nel proprio ambiente, questa differenza dipenderà da altri fattori. Questi fattori includono la scelta del formato dell'evento, la dimensione degli eventi/messaggi di input, le chiavi di partizione e la query.

![Confronto Azure Cosmos DB metrica](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Con 1,2, l'analisi di flusso è più intelligente nell'utilizzo del 100% della velocità effettiva disponibile in Azure Cosmos DB con pochissimi invii di richieste di limitazione o limitazione della frequenza. Ciò offre un'esperienza migliore per gli altri carichi di lavoro, ad esempio le query in esecuzione nel contenitore allo stesso tempo. Per informazioni sull'aumento delle dimensioni di analisi di flusso con Azure Cosmos DB come sink per 1.000 a 10.000 messaggi al secondo, provare [questo progetto di esempio di Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

La velocità effettiva dell'output Azure Cosmos DB è identica a 1,0 e 1,1. Si *consiglia vivamente* di usare il livello di compatibilità 1,2 in analisi di flusso con Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Impostazioni Azure Cosmos DB per l'output JSON

L'uso di Azure Cosmos DB come output in analisi di flusso genera la richiesta seguente per le informazioni.

![Campi di informazioni per un flusso di output Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Description|
|-------------   | -------------|
|Alias di output    | Alias che fa riferimento a questo output nella query di analisi di flusso.|
|Sottoscrizione    | Sottoscrizione di Azure.|
|Account ID      | Nome o URI endpoint dell'account Azure Cosmos DB.|
|Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB.|
|Database        | Nome del database Azure Cosmos DB.|
|Nome contenitore | Nome del contenitore, ad esempio `MyContainer`. Deve esistere un contenitore denominato `MyContainer`.  |
|Document ID     | Facoltativa. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lo si lascia vuoto, verranno inseriti tutti gli eventi, senza alcuna opzione di aggiornamento.|

Dopo aver configurato l'output del Azure Cosmos DB, è possibile utilizzarlo nella query come destinazione di un' [istruzione into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando si usa un output Azure Cosmos DB in questo modo, è [necessario impostare in modo esplicito una chiave di partizione](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

Il record di output deve contenere una colonna con distinzione tra maiuscole e minuscole denominata dopo la chiave di partizione in Azure Cosmos DB. Per ottenere una maggiore parallelizzazione, l'istruzione potrebbe richiedere una [clausola PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) che utilizza la stessa colonna.

Ecco una query di esempio:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Gestione degli errori e tentativi

Se si verifica un errore temporaneo, la mancata disponibilità del servizio o la limitazione quando l'analisi di flusso invia eventi a Azure Cosmos DB, l'analisi di flusso ritenta a tempo indefinito per completare correttamente l'operazione. Ma non tenta di eseguire nuovi tentativi per gli errori seguenti:

- Non autorizzato (codice errore HTTP 401)
- NotFound (codice errore HTTP 404)
- Forbidden (codice errore HTTP 403)
- Richiesta non valida (codice errore HTTP 400)
