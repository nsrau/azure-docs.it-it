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
ms.openlocfilehash: 5b28d75e6526f27fd0076244ec32848dbf20e91e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424775"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
Analisi di flusso di Azure può usare [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione. Si consiglia di impostare il processo sul livello di compatibilità 1,2 quando si usa Azure Cosmos DB come output.

Se non si ha familiarità con Azure Cosmos DB, vedere la [documentazione di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) per iniziare. 

> [!Note]
> Ad oggi, Analisi di flusso supporta la connessione ad Azure Cosmos DB solo tramite l'*API SQL*.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Nozioni di base di Azure Cosmos DB come destinazione di output
L'output di Azure Cosmos DB in Analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nei contenitori di Azure Cosmos DB. 

Analisi di flusso non crea contenitori nel database. Al contrario, richiede che l'utente li crei anticipatamente. È quindi possibile controllare i costi di fatturazione dei contenitori di Azure Cosmos DB. È anche possibile ottimizzare le prestazioni, la coerenza e la capacità dei contenitori direttamente usando l'[API di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Le sezioni seguenti illustrano in dettaglio alcune opzioni del contenitore per Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
In base ai requisiti dell'applicazione, Azure Cosmos DB consente di ottimizzare il database e i contenitori e di bilanciare coerenza, disponibilità, latenza e velocità effettiva. 

A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. È possibile migliorare la velocità effettiva aumentando le unità richiesta (UR) nel contenitore. 

Per impostazione predefinita, Azure Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nel contenitore. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di Azure Cosmos DB. 

Per altre informazioni, rivedere l'articolo [Modificare i livelli di coerenza del database e delle query](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di Analisi di flusso di Azure con Azure Cosmos DB consente di inserire o aggiornare i record nel contenitore in base a una determinata colonna **ID documento**. Il valore restituito viene definito anche *upsert*.

Analisi di flusso usa un approccio upsert ottimistico. Gli aggiornamenti si verificano solo quando un inserimento ha esito negativo con un conflitto a livello di ID documento. 

Con il livello di compatibilità 1.0, Analisi di flusso esegue questo aggiornamento come operazione PATCH, in modo da consentire aggiornamenti parziali al documento. Analisi di flusso aggiunge nuove proprietà o sostituisce in modo incrementale una proprietà esistente. Le modifiche apportate ai valori delle proprietà di matrice nel documento JSON comportano, tuttavia, la sovrascrittura dell'intera matrice, ovvero, non viene eseguito il merge della matrice. 

Con 1.2, il comportamento di upsert viene modificato per inserire o sostituire il documento. La sezione successiva sul livello di compatibilità 1.2 descrive ulteriormente questo comportamento.

Se il documento JSON in ingresso dispone di un campo ID esistente, il campo viene usato automaticamente come colonna di **ID documento** in Azure Cosmos DB. Tutte le scritture successive vengono gestite come tali, causando una delle situazioni seguenti:

- Gli ID univoci generano operazioni di inserimento.
- ID duplicati e **ID documento** impostati su **ID** generano operazioni di upsert.
- ID duplicati e **ID documento** non impostati generano un errore dopo il primo documento.

Se si desidera salvare *tutti* i documenti, inclusi quelli che hanno un ID duplicato, rinominare il campo ID nella query (usando la parola chiave **AS**). Consentire ad Azure Cosmos DB di creare il campo ID o sostituire l'ID con il valore di un'altra colonna (usando la parola chiave **AS** o usando l'impostazione**ID documento**).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partizionamento dei dati in Azure Cosmos DB
Con Azure Cosmos DB le partizioni vengono scalate automaticamente in base al carico di lavoro. Sono quindi consigliabili contenitori [illimitati ](../cosmos-db/partition-data.md) come approccio per il partizionamento dei dati. Durante la scrittura in contenitori illimitati, Analisi di flusso di Azure usa un numero di writer paralleli uguale a quello usato nel passaggio di query precedente o nello schema di partizionamento di input.

> [!NOTE]
> Analisi di flusso di Azure supporta solo un numero illimitato di contenitori con chiavi di partizione di primo livello. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate (ad esempio `/region/name`) non sono supportate. 

A seconda della chiave di partizione scelta, è possibile che venga visualizzato questo _avviso_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

È importante scegliere una proprietà della chiave di partizione con un numero di valori distinti e che consente di distribuire il carico di lavoro in modo uniforme tra questi valori. Come artefatto naturale del partizionamento, le richieste che coinvolgono la stessa chiave di partizione sono limitate dalla velocità effettiva massima di una singola partizione. 

Le dimensioni di archiviazione per i documenti che appartengono allo stesso valore della chiave di partizione sono limitate a 20 GB (il [limite delle dimensioni della partizione fisica](../cosmos-db/partition-data.md) è 50 GB). Una [chiave di partizione ideale](../cosmos-db/partitioning-overview.md#choose-partitionkey) viene visualizzata spesso come filtro nelle query e ha una cardinalità sufficiente per garantire la scalabilità della soluzione.

Le chiavi di partizione usate per le query di analisi di flusso e Cosmos DB non devono essere identiche. Le topologie completamente parallele consigliano di usare la *chiave di partizione di input*, `PartitionId` , come chiave di partizione della query di analisi di flusso, ma che potrebbe non essere la scelta consigliata per la chiave di partizione di un contenitore Cosmos DB.

Una chiave di partizione è anche il limite per le transazioni in stored procedure e trigger per Azure Cosmos DB. È necessario scegliere la chiave di partizione in modo che i documenti che si verificano insieme nelle transazioni condividano lo stesso valore della chiave di partizione. L'articolo [Partizionamento in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) fornisce altri dettagli sulla scelta di una chiave di partizione.

Per i contenitori di Azure Cosmos DB fissi, Analisi di flusso di Azure non consente alcuna possibilità di ridimensionamento quando sono al completo. Tali contenitori hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR al secondo. Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore illimitato, ad esempio con una velocità effettiva di almeno 1000 UR al secondo e una chiave di partizione, usare lo [strumento di migrazione dati](../cosmos-db/import-data.md) o la [libreria di feed di modifiche](../cosmos-db/change-feed.md).

La possibilità di scrivere in più contenitori fissi è in fase di deprecazione. Non è consigliabile per la scalabilità orizzontale del processo di Analisi di flusso.

## <a name="improved-throughput-with-compatibility-level-12"></a>Velocità effettiva migliorata con il livello di compatibilità 1.2
Con il livello di compatibilità 1.2, Analisi di flusso supporta l'integrazione nativa per la scrittura bulk in Azure Cosmos DB. Questa integrazione consente di scrivere in modo efficace in Azure Cosmos DB massimizzando la velocità effettiva e gestendo in modo efficiente la limitazione delle richieste. 

Il meccanismo di scrittura migliorato è disponibile con un nuovo livello di compatibilità a causa della differenza nel comportamento di upsert. Con i livelli precedenti a 1.2, il comportamento di upsert consiste nell'inserimento o nell'unione del documento. Con 1.2, il comportamento di upsert viene modificato per inserire o sostituire il documento.

Con i livelli precedenti a 1.2, Analisi di flusso usa una stored procedure personalizzata per eseguire l'upsert bulk dei documenti per ogni chiave di partizione in Azure Cosmos DB. Un batch viene scritto come transazione. Anche quando un singolo record raggiunge un errore temporaneo (limitazione), è necessario ritentare l'intero batch. Questo rende gli scenari con una limitazione ancora ragionevole relativamente lenti.

L'esempio seguente mostra due processi di Analisi di flusso identici che leggono dallo stesso input di Hub eventi di Azure. Entrambi i processi di Analisi di flusso sono [completamente partizionati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) con una query pass-through e scrivono in contenitori di Azure Cosmos DB identici. Le metriche a sinistra provengono dal processo configurato con il livello di compatibilità 1.0. Le metriche a destra sono configurate con 1.2. Una chiave di partizione di un contenitore Azure Cosmos DB è un GUID univoco che proviene dall'evento di input.

![Confronto tra le metriche di Analisi di flusso](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

La frequenza degli eventi in ingresso in Hub eventi è due volte superiore rispetto alla configurazione di esecuzione dei contenitori di Azure Cosmos DB (20.000 UR), perciò in Azure Cosmos DB è prevista la limitazione delle richieste. Tuttavia, il processo con 1.2 sta scrivendo in modo coerente a una velocità effettiva superiore (eventi di output al minuto) e con una media inferiore di % di utilizzo unità di streaming. Nel proprio ambiente, questa differenza dipenderà da altri fattori. Questi fattori includono la scelta del formato dell'evento, la dimensione degli eventi/messaggi di input, le chiavi di partizione e la query.

![Confronto di metriche di Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Con 1.2, aumenta l'intelligenza di Analisi di flusso nell'utilizzo del 100% della velocità effettiva disponibile in Azure Cosmos DB con pochissimi invii di limitazione delle richieste o limitazione della frequenza. Ciò fornisce un'esperienza migliore per gli altri carichi di lavoro, ad esempio le query in esecuzione nel contenitore contemporaneamente. Per informazioni sull'aumento delle dimensioni di Analisi di flusso con Azure Cosmos DB come sink da 1.000 fino a 10.000 messaggi al secondo, provare [questo progetto di esempio di Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

La velocità effettiva dell'output Azure Cosmos DB è identica a 1.0 e 1.1. È *consigliabile* usare il livello di compatibilità 1.2 in Analisi di flusso con Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Impostazioni di Cosmos DB per l'output JSON

L'uso di Cosmos DB come output in Analisi di flusso genera la seguente richiesta di informazioni.

![Campi di informazioni per un flusso di output Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrizione|
|-------------   | -------------|
|Alias di output    | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure.|
|Subscription    | Sottoscrizione di Azure.|
|Account ID      | Nome o URI endpoint dell'account Azure Cosmos DB.|
|Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB.|
|Database        | Nome del database Azure Cosmos DB.|
|Nome contenitore | Il nome del contenitore, ad esempio `MyContainer`. Deve esistere un contenitore denominato `MyContainer`.  |
|Document ID     | Facoltativa. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lasciato vuoto, tutti gli eventi verranno inseriti senza alcuna opzione di aggiornamento.|

Dopo aver configurato l'output di Azure Cosmos DB, è possibile usarlo nella query come destinazione di un'[istruzione INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando si usa un output di Azure Cosmos DB in questo modo, [una chiave di partizione deve essere impostata in modo esplicito](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

Il record di output deve contenere una colonna con distinzione tra maiuscole e minuscole denominata in base alla chiave di partizione in Azure Cosmos DB. Per ottenere una maggiore parallelizzazione, è possibile che l'istruzione richieda una [clausola PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) che usa la stessa colonna.

Ecco una query di esempio:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Gestione degli errori e tentativi

Se si verifica un errore temporaneo, la indisponibilità del servizio o la limitazione delle richieste quando Analisi di flusso invia eventi ad Azure Cosmos DB, Analisi di flusso ritenta a tempo indefinito di completare correttamente l'operazione. Non tenta invece di eseguire nuovi tentativi per gli errori seguenti:

- Unauthorized (codice errore HTTP 401)
- NotFound (codice errore HTTP 404)
- Forbidden (codice errore HTTP 403)
- BadRequest (codice errore HTTP 400)

## <a name="common-issues"></a>Problemi comuni

1. Un vincolo di indice univoco viene aggiunto alla raccolta e i dati di output di analisi di flusso violano questo vincolo. Assicurarsi che i dati di output di analisi di flusso non violino i vincoli UNIQUE o rimuovere i vincoli. Per ulteriori informazioni, vedere [vincoli UNIQUE KEY in Azure Cosmos DB](../cosmos-db/unique-keys.md).

2. La `PartitionKey` colonna non esiste.

3. La `Id` colonna non esiste.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md) 
* [Output di Analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md)
* [Partizionamento dell'output dei BLOB personalizzato in Analisi di flusso di Azure](stream-analytics-custom-path-patterns-blob-storage-output.md)
