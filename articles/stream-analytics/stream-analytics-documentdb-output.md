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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254443"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
Analisi di flusso di Azure può essere destinato al database [Cosmos](https://azure.microsoft.com/services/documentdb/) di Azure per l'output JSON, consentendo l'archiviazione dei dati e query a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Azure Cosmos DB, vedere la documentazione di [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) per iniziare. 

> [!Note]
> A questo punto, Analisi di flusso supporta la connessione a Database Cosmos di Azure solo tramite *l'API SQL.*
> Altre API di Azure Cosmos DB non sono ancora supportate. Se si punta Analisi di flusso agli account di Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Nozioni di base di Azure Cosmos DB come destinazione di output
L'output del database Cosmos di Azure in Analisi di flusso consente di scrivere i risultati dell'elaborazione del flusso come output JSON nei contenitori del database Cosmos di Azure.The Azure Cosmos DB output in Stream Analytics enables writing your stream processing results as JSON output into your Azure Cosmos DB containers. 

Analisi di flusso non crea contenitori nel database. Invece, richiede di crearli in anticipo. È quindi possibile controllare i costi di fatturazione dei contenitori di database Cosmos di Azure.You can then control the billing costs of Azure Cosmos DB containers. È anche possibile ottimizzare le prestazioni, la coerenza e la capacità dei contenitori direttamente usando le API del database [di Azure Cosmos.](https://msdn.microsoft.com/library/azure/dn781481.aspx)

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Le sezioni seguenti illustrano in dettaglio alcune delle opzioni del contenitore per Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Ottimizzazione di coerenza, disponibilità e latenza
Per soddisfare i requisiti dell'applicazione, Azure Cosmos DB consente di ottimizzare il database e i contenitori e di effettuare compromessi tra coerenza, disponibilità, latenza e velocità effettiva. 

A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. È possibile migliorare la velocità effettiva aumentando le unità richiesta (RU) nel contenitore. 

Inoltre, per impostazione predefinita, Il database Cosmos di Azure abilita l'indicizzazione sincrona in ogni operazione CRUD nel contenitore. Questa è un'altra opzione utile per controllare le prestazioni di scrittura/lettura in Azure Cosmos DB. 

Per altre informazioni, vedere l'articolo Modificare i livelli di coerenza del database e delle [query.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di Analisi di flusso con Azure Cosmos DB consente di inserire o aggiornare record nel contenitore in base a una determinata colonna **ID documento.** Questo è anche chiamato un *upsert*.

Analisi di flusso usa un approccio upsert ottimistico. Gli aggiornamenti vengono eseguiti solo quando un inserimento non riesce in caso di conflitto di ID documento. 

Con il livello di compatibilità 1.0, Analisi di flusso esegue questo aggiornamento come operazione PATCH, in modo da abilitare gli aggiornamenti parziali al documento. Analisi di flusso aggiunge nuove proprietà o sostituisce una proprietà esistente in modo incrementale. Tuttavia, le modifiche apportate ai valori delle proprietà della matrice nel documento JSON comportano la sovrascrittura dell'intera matrice. Ovvero, la matrice non viene unita. 

Con 1.2, il comportamento di upsert viene modificato per inserire o sostituire il documento. Nella sezione successiva sul livello di compatibilità 1.2 viene descritto ulteriormente questo comportamento.

Se il documento JSON in ingresso include un campo ID esistente, tale campo viene usato automaticamente come colonna **ID documento** in Azure Cosmos DB. Tutte le scritture successive vengono gestite come tali, portando a una di queste situazioni:Any subsequent writes are handled as such, leading to one of these situations:

- ID univoci portano all'inserimento.
- ID duplicati e **ID documento** impostati su **ID** lead su upsert.
- ID duplicati e **ID documento** non impostato lead su errore, dopo il primo documento.

Se si desidera salvare *tutti i* documenti, inclusi quelli con un ID duplicato, rinominare il campo ID nella query (utilizzando la parola chiave **AS).** Consentire a Azure Cosmos DB di creare il campo ID o sostituire l'ID con il valore di un'altra colonna (usando la parola chiave **AS** o l'impostazione **ID documento).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partizionamento dei dati in Azure Cosmos DBData partitioning in Azure Cosmos DB
Azure Cosmos DB ridimensiona automaticamente le partizioni in base al carico di lavoro. Pertanto, è consigliabile utilizzare contenitori [illimitati](../cosmos-db/partition-data.md) come approccio per il partizionamento dei dati. Quando Analisi di flusso scrive in contenitori illimitati, usa un numero di writer paralleli pari al passaggio di query precedente o allo schema di partizionamento dell'input.

> [!NOTE]
> Analisi di flusso di Azure supporta solo contenitori illimitati con chiavi di partizione al livello superiore. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate, `/region/name`ad esempio , non sono supportate. 

A seconda della chiave di partizione scelta, è possibile che venga visualizzato questo _avviso:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

È importante scegliere una proprietà della chiave di partizione con un numero di valori distinti e che consente di distribuire il carico di lavoro in modo uniforme tra questi valori. Come elemento naturale del partizionamento, le richieste che coinvolgono la stessa chiave di partizione sono limitate dalla velocità effettiva massima di una singola partizione. 

La dimensione di archiviazione per i documenti che appartengono alla stessa chiave di partizione è limitata a 10 GB. Una chiave di partizione ideale è quella che viene visualizzata frequentemente come filtro nelle query e ha una cardinalità sufficiente per garantire che la soluzione sia scalabile.

Una chiave di partizione è anche il limite per le transazioni nelle stored procedure e nei trigger per Azure Cosmos DB. È consigliabile scegliere la chiave di partizione in modo che i documenti che si verificano insieme nelle transazioni condividano lo stesso valore della chiave di partizione. L'articolo [Partizionamento in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) offre ulteriori dettagli sulla scelta di una chiave di partizione.

Per i contenitori di database Cosmos di Azure fissi, Analisi di flusso non consente la scalabilità verticale o orizzontale dopo che sono pieni. Hanno un limite massimo di 10 GB e 10.000 RU/s di velocità effettiva. Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore illimitato (ad esempio, uno con almeno 1.000 RU/s e una chiave di partizione), utilizzare [lo strumento](../cosmos-db/import-data.md) di migrazione dei dati o la libreria del feed di [modifiche.](../cosmos-db/change-feed.md)

La possibilità di scrivere in più contenitori fissi è deprecata. Non è consigliabile per la scalabilità orizzontale del processo di Analisi di flusso.

## <a name="improved-throughput-with-compatibility-level-12"></a>Miglioramento della velocità effettiva con livello di compatibilità 1.2
Con il livello di compatibilità 1.2, Analisi di flusso supporta l'integrazione nativa per la scrittura in blocco in Azure Cosmos DB. Questa integrazione consente di scrivere in modo efficace in Azure Cosmos DB massimizzando la velocità effettiva e limitando in modo efficiente le richieste. 

Il meccanismo di scrittura migliorato è disponibile in base a un nuovo livello di compatibilità a causa di una differenza nel comportamento di upsert. Con i livelli precedenti alla 1.2, il comportamento di upsert consiste nell'inserire o unire il documento. Con 1.2, il comportamento di upsert viene modificato per inserire o sostituire il documento.

Con i livelli precedenti alla 1.2, Analisi di flusso usa una stored procedure personalizzata per eseguire il bulking dei documenti per chiave di partizione in Database cosmos di Azure.With levels before 1.2, Stream Analytics uses a custom stored procedure to bulksert documents per partition key into Azure Cosmos DB. Lì, un batch viene scritto come una transazione. Anche quando un singolo record raggiunge un errore temporaneo (limitazione), l'intero batch deve essere ritentato. Questo rende gli scenari con limitazione anche ragionevole relativamente lento.

L'esempio seguente mostra due processi di Analisi di flusso identici che leggono dallo stesso input di Hub eventi di Azure.The following example shows two identical Stream Analytics jobs reading from the same Azure Event Hubs input. Entrambi i processi di Analisi di flusso sono [completamente partizionati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) con una query pass-through e scrivono in contenitori di database Cosmos di Azure identici. Le metriche a sinistra provengono dal processo configurato con il livello di compatibilità 1.0.Metrics on the left are from the job configured with compatibility level 1.0. Le metriche a destra sono configurate con 1.2. La chiave di partizione di un contenitore di database Cosmos di Azure è un GUID univoco proveniente dall'evento di input.

![Confronto delle metriche di Analisi di flusso](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

La frequenza degli eventi in ingresso negli hub eventi è due volte superiore a quella dei contenitori del database Cosmos di Azure (20.000 RU) configurati per l'acquisizione, pertanto è prevista la limitazione in Azure Cosmos DB. Tuttavia, il processo con 1.2 è costantemente la scrittura a una velocità effettiva superiore (eventi di output al minuto) e con un utilizzo medio su% inferiore. Nel proprio ambiente, questa differenza dipenderà da pochi altri fattori. Questi fattori includono la scelta del formato dell'evento, la dimensione dell'evento/messaggio di input, le chiavi di partizione e la query.

![Confronto delle metriche di Azure Cosmos DBAzure Cosmos DB metrics comparison](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Con 1.2, Analisi di flusso è più intelligente nell'uso del 100% della velocità effettiva disponibile in Azure Cosmos DB con pochissimi nuovi invii dalla limitazione delle richieste o dalla limitazione della velocità. Ciò offre un'esperienza migliore per altri carichi di lavoro, ad esempio le query in esecuzione nel contenitore contemporaneamente. Se si vuole vedere come Analisi di flusso si adatta con Azure Cosmos DB come sink per 1.000 a 10.000 messaggi al secondo, provare questo progetto di esempio di [Azure.](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)

La velocità effettiva dell'output del database Cosmos di Azure è identica a 1.0 e 1.1.Throughput of Azure Cosmos DB output is identical with 1.0 and 1.1. *È consigliabile* usare il livello di compatibilità 1.2 in Analisi di flusso con Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Impostazioni del database Cosmos di Azure per l'output JSONAzure Cosmos DB settings for JSON output

L'uso di Azure Cosmos DB come output in Analisi di flusso genera il prompt di informazioni seguente.

![Campi di informazioni per un flusso di output di Azure Cosmos DBInformation fields for an Azure Cosmos DB output stream](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrizione|
|-------------   | -------------|
|Alias di output    | Un alias per fare riferimento a questo output nella query di Analisi di flusso.|
|Subscription    | Sottoscrizione di Azure.|
|Account ID      | Nome o URI endpoint dell'account Azure Cosmos DB.|
|Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB.|
|Database        | Nome del database Azure Cosmos DB.|
|Nome contenitore | Nome del contenitore, ad esempio `MyContainer`. Deve esistere `MyContainer` un contenitore denominato.  |
|Document ID     | Facoltativa. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lo si lascia vuoto, verranno inseriti tutti gli eventi, senza alcuna opzione di aggiornamento.|

Dopo aver configurato l'output del database Cosmos di Azure, è possibile usarlo nella query come destinazione di [un'istruzione INTO.](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics) Quando si usa un output di Azure Cosmos DB in questo modo, una chiave di [partizione deve essere impostata in modo esplicito.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

Il record di output deve contenere una colonna con distinzione tra maiuscole e minuscole denominata in onore della chiave di partizione in Azure Cosmos DB. Per ottenere una maggiore parallelizzazione, l'istruzione potrebbe richiedere una [clausola PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) che utilizza la stessa colonna.

Ecco una query di esempio:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Gestione degli errori e tentativi

Se si verifica un errore temporaneo, l'indisponibilità del servizio o la limitazione delle richieste mentre Analisi di flusso invia eventi a Database Cosmos di Azure, Analisi di flusso riprova a tempo indeterminato per completare correttamente l'operazione. Ma non tenta tentativi per i seguenti errori:

- Non autorizzato (codice di errore HTTP 401)
- NotFound (codice di errore HTTP 404)
- Accesso negato (codice di errore HTTP 403)
- BadRequest (codice di errore HTTP 400)
