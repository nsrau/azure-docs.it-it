---
title: Output di Analisi di flusso di Azure in Cosmos DB
description: Questo articolo descrive come usare Analisi di flusso di Azure per salvare output JSON in Azure Cosmos DB, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560192"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
L'analisi di flusso può usare [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Cosmos DB, vedere l'articolo che descrive il [percorso di apprendimento di Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per un’introduzione. 

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta solo la connessione ad Azure Cosmos DB tramite l'**API SQL**.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Nozioni di base di Cosmos DB come destinazione di output
L'output Azure Cosmos DB in analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nei contenitori di Cosmos DB. Analisi di flusso non crea contenitori nel database, ma è necessario crearli in anticipo. In questo modo, i costi di fatturazione dei contenitori Cosmos DB sono controllati dall'utente e quindi è possibile ottimizzare le prestazioni, la coerenza e la capacità dei contenitori usando direttamente le [API di Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Di seguito sono elencate alcune delle opzioni del contenitore Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
Per soddisfare i requisiti dell'applicazione, Azure Cosmos DB consente di ottimizzare il database e i contenitori e di effettuare compromessi tra coerenza, disponibilità, latenza e velocità effettiva. A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. La velocità effettiva può essere migliorata scalando le unità richiesta (UR) nel contenitore. Per impostazione predefinita, Azure Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nel contenitore. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di Azure Cosmos DB. Per altre informazioni, rivedere l'articolo relativo a come [modificare i livelli di coerenza del database e delle query](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di analisi di flusso con Azure Cosmos DB consente di inserire o aggiornare i record nel contenitore in base a una determinata colonna ID documento. Questa implementazione è detta anche *upsert*.

Analisi di flusso di Azure usa un approccio upsert ottimistico in cui gli aggiornamenti vengono eseguiti solo quando l'inserimento non riesce a causa di un conflitto di ID documento. Con il livello di compatibilità 1,0, questo aggiornamento viene eseguito come PATCH, quindi Abilita gli aggiornamenti parziali del documento, ovvero l'aggiunta di nuove proprietà o la sostituzione di una proprietà esistente, che viene eseguita in modo incrementale. Le modifiche apportate ai valori delle proprietà di matrice nel documento JSON comportano, tuttavia, la sovrascrittura dell'intera matrice, ovvero non viene eseguito il merge della matrice. Con 1,2, il comportamento di Upsert viene modificato per inserire o sostituire il documento. Questa procedura è descritta più avanti nella sezione relativa al livello di compatibilità 1,2 riportata di seguito.

Se il documento JSON in ingresso include già un campo ID esistente, tale campo verrà usato automaticamente come colonna ID documento in Cosmos DB e le eventuali scritture successive verranno gestite come tali. Questo scenario sarà caratterizzato dalle situazioni seguenti:
- gli ID univoci generano operazioni di inserimento
- ID duplicati e 'ID documento' impostato su 'ID' generano operazioni di upsert
- ID duplicati e 'ID documento' non impostato generano un errore dopo il primo documento

Se si desidera salvare <i>tutti</i> i documenti inclusi quelli con un ID duplicato, rinominare il campo ID nella query (con la parola chiave AS) e consentire a Cosmos DB di creare il campo ID o sostituire l'ID con un altro valore della colonna (usando la parola chiave AS o tramite l'impostazione di 'ID documento').

## <a name="data-partitioning-in-cosmos-db"></a>Partizionamento dei dati in Cosmos DB
I contenitori [senza limiti](../cosmos-db/partition-data.md) di Azure Cosmos DB costituiscono l'approccio consigliato per il partizionamento dei dati, in quanto Azure Cosmos DB ridimensiona automaticamente le partizioni in base al carico di lavoro. Durante la scrittura in contenitori senza limiti, Analisi di flusso di Azure usa un numero di writer paralleli uguale a quello usato nel passaggio di query precedente o nello schema di partizionamento di input.
> [!NOTE]
> A questo punto, analisi di flusso di Azure supporta solo contenitori illimitati con chiavi di partizione al primo livello. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate (ad esempio `/region/name`) non sono supportate. 

A seconda della scelta della chiave di partizione, è possibile che venga visualizzato il _messaggio di avviso_seguente:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

È importante scegliere una proprietà della chiave di partizione con un numero di valori distinti e consente di distribuire il carico di lavoro in modo uniforme tra questi valori. Come artefatto naturale del partizionamento, le richieste che interessano la stessa chiave di partizione sono limitate dalla velocità effettiva massima di una singola partizione. Inoltre, la dimensione di archiviazione per i documenti che appartengono alla stessa chiave di partizione è limitata a 10 GB. Una chiave di partizione ideale appare spesso come filtro nelle query e ha una cardinalità sufficiente per garantire la scalabilità della soluzione.

Una chiave di partizione è anche il limite per le transazioni nelle stored procedure e nei trigger di DocumentDB. È necessario scegliere la chiave di partizione in modo che i documenti che si verificano insieme nelle transazioni condividano lo stesso valore della chiave di partizione. Per informazioni dettagliate sulla scelta di una chiave di partizione, in Cosmos DB viene visualizzato il [partizionamento](../cosmos-db/partitioning-overview.md) degli articoli.

Per i contenitori Azure Cosmos DB fissi, l'analisi di flusso non consente di aumentare o ridurre le prestazioni una volta completate. Tali raccolte hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR al secondo.  Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore senza limiti, ad esempio con una velocità effettiva di almeno 1000 UR al secondo e una chiave di partizione, è necessario usare l'[utilità di migrazione dati](../cosmos-db/import-data.md) o la [libreria di feed di modifiche](../cosmos-db/change-feed.md).

La possibilità di scrivere in più contenitori fissi è deprecata e non è consigliata per la scalabilità orizzontale del processo di analisi di flusso.

## <a name="improved-throughput-with-compatibility-level-12"></a>Velocità effettiva migliorata con il livello di compatibilità 1,2
Con il livello di compatibilità 1,2, analisi di flusso supporta l'integrazione nativa per la scrittura bulk in Cosmos DB. In questo modo, la scrittura viene abilitata per Cosmos DB con l'ottimizzazione della velocità effettiva e la gestione efficiente delle richieste di limitazione. Il meccanismo di scrittura migliorato è disponibile con un nuovo livello di compatibilità a causa della differenza di comportamento del Upsert.  Prima del 1,2, il comportamento di Upsert consiste nell'inserire o unire il documento. Con 1,2, il comportamento di Upsert viene modificato per inserire o sostituire il documento.

Prima del 1,2, usa un stored procedure personalizzato per eseguire in blocco i documenti Upsert per ogni chiave di partizione in Cosmos DB, in cui un batch viene scritto come transazione. Anche quando un singolo record raggiunge un errore temporaneo (limitazione), è necessario ritentare l'intero batch. Questo ha reso gli scenari con una limitazione anche ragionevole relativamente più lenta. Il confronto seguente mostra il comportamento di tali processi con 1,2.

L'esempio seguente mostra due processi di analisi di flusso identici che leggono dallo stesso input dell'hub eventi. Entrambi i processi di analisi di flusso sono [completamente partizionati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) con una query passthrough e scrivono in contenitori CosmosDB identici. Le metriche a sinistra sono rilasciate dal processo configurato con il livello di compatibilità 1,0 e quelle a destra sono configurate con 1,2. La chiave di partizione di un contenitore Cosmos DB è un GUID univoco proveniente dall'evento di input.

![confronto delle metriche di analisi di flusso](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

La frequenza degli eventi in ingresso nell'hub eventi è di 2x superiore a quella dei contenitori Cosmos DB (20.000 UR) sono configurati per l'assunzione, quindi la limitazione è prevista in Cosmos DB. Tuttavia, il processo con 1,2, sta scrivendo in modo coerente a una velocità effettiva superiore (eventi di output al minuto) e con un valore di percentuale di utilizzo medio inferiore. Nel proprio ambiente, questa differenza dipenderà da altri fattori, ad esempio la scelta del formato dell'evento, la dimensione degli eventi/messaggi di input, le chiavi di partizione, le query e così via.

![confronto delle metriche di Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Con 1,2, l'analisi di flusso è più intelligente nell'utilizzo del 100% della velocità effettiva disponibile in Cosmos DB con pochissimi invii di richieste di limitazione/limitazione della frequenza. Ciò offre un'esperienza migliore per gli altri carichi di lavoro, ad esempio le query in esecuzione nel contenitore allo stesso tempo. Se è necessario provare a eseguire la scalabilità orizzontale con Cosmos DB come sink per i messaggi da 1.000 a 10.000 al secondo, ecco un [progetto di esempi di Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) che consente di eseguire questa operazione.
Si noti che Cosmos DB velocità effettiva di output è identica a 1,0 e 1,1. Dal momento che 1,2 non è il valore predefinito, è possibile [impostare il livello di compatibilità](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) per un processo di analisi di flusso usando il portale o la chiamata all' [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)per la creazione di processi. Si *consiglia vivamente* di usare il livello di compatibilità 1,2 in ASA con Cosmos DB.



## <a name="cosmos-db-settings-for-json-output"></a>Impostazioni di Cosmos DB per l'output JSON

La creazione di Cosmos DB come output nell'analisi di flusso genera una richiesta di informazioni, come illustrato di seguito. Questa sezione fornisce una spiegazione della definizione delle proprietà.

![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Description|
|-------------   | -------------|
|Alias di output    | Un alias per fare riferimento a questo output nella query ASA.|
|Sottoscrizione    | Scegliere la sottoscrizione di Azure.|
|Account ID      | Nome o URI endpoint dell'account Azure Cosmos DB.|
|Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB.|
|Database        | Nome del database Azure Cosmos DB.|
|Nome contenitore | Nome del contenitore da usare. `MyContainer` è un input valido di esempio: è necessario che esista un contenitore denominato `MyContainer`.  |
|Document ID     | facoltativo. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lasciato vuoto, tutti gli eventi verranno inseriti senza alcuna opzione di aggiornamento.|

Una volta configurato l'output del Cosmos DB, è possibile utilizzarlo nella query come destinazione di un' [istruzione into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando si usa un output Cosmos DB come tale, [è necessario impostare in modo esplicito una chiave di partizione](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). Il record di output deve contenere una colonna con distinzione tra maiuscole e minuscole denominata dopo la chiave di partizione in Cosmos DB. Per ottenere una maggiore parallelizzazione, è possibile che l'istruzione richieda una [clausola PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) usando la stessa colonna.

**Query di esempio**:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Gestione degli errori e tentativi

In caso di errori temporanei, l'indisponibilità del servizio o la limitazione durante l'invio di eventi a Cosmos DB, l'analisi di flusso esegue un nuovo tentativo per completare correttamente l'operazione. Non vengono però effettuati ulteriori tentativi per gli errori seguenti:

- Non autorizzato (codice errore HTTP 401)
- NotFound (codice errore HTTP 404)
- Forbidden (codice errore HTTP 403)
- Richiesta non valida (codice errore HTTP 400)
