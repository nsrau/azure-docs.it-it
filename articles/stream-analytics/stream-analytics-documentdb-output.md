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
ms.openlocfilehash: de5febaeecd176a8718364720132d3fa4433c57f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443618"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
L'analisi di flusso può usare [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Cosmos DB, vedere l'articolo che descrive il [percorso di apprendimento di Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per un’introduzione. 

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta solo la connessione ad Azure Cosmos DB tramite l'**API SQL**.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Nozioni di base di Cosmos DB come destinazione di output
L'output di Azure Cosmos DB in Stream Analitica consente la scrittura del flusso di elaborazione dei risultati come output JSON in uno o più contenitori di Cosmos DB. Stream Analitica non crea i contenitori nel database, invece che sia necessario creare in anticipo. Si tratta in modo che i costi di fatturazione dei contenitori Cosmos DB vengono controllati dall'utente e in modo che è possibile ottimizzare le prestazioni, coerenza e la capacità dei contenitori direttamente usando il [API di Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Di seguito sono descritte alcune delle opzioni di contenitore di Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
In base ai requisiti dell'applicazione, Azure Cosmos DB ti permette di ottimizzare il database e i contenitori e ottenere un buon compromesso tra coerenza, disponibilità, latenza e velocità effettiva. A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. Velocità effettiva può essere migliorata aumentando Units(RUs) richiesta per il contenitore. Per impostazione predefinita, Azure Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nel contenitore. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di Azure Cosmos DB. Per altre informazioni, rivedere l'articolo relativo a come [modificare i livelli di coerenza del database e delle query](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione Analitica Stream con Azure Cosmos DB consente di inserire o aggiornare i record in un contenitore basato su una determinata colonna ID documento. Questa implementazione è detta anche *upsert*.

Analisi di flusso di Azure usa un approccio upsert ottimistico in cui gli aggiornamenti vengono eseguiti solo quando l'inserimento non riesce a causa di un conflitto di ID documento. Con il livello di compatibilità 1.0, questo aggiornamento viene eseguito come una PATCH, consentendo aggiornamenti parziali al documento, vale a dire, l'aggiunta di nuove proprietà o la sostituzione di che una proprietà esistente viene eseguita in modo incrementale. Le modifiche apportate ai valori delle proprietà di matrice nel documento JSON comportano, tuttavia, la sovrascrittura dell'intera matrice, ovvero non viene eseguito il merge della matrice. 1\.2, per inserire o sostituire il documento viene modificato il comportamento di upsert. Questo è descritto più dettagliatamente nella sezione 1.2 a livello di compatibilità di seguito.

Se il documento JSON in ingresso include già un campo ID esistente, tale campo verrà usato automaticamente come colonna ID documento in Cosmos DB e le eventuali scritture successive verranno gestite come tali. Questo scenario sarà caratterizzato dalle situazioni seguenti:
- gli ID univoci generano operazioni di inserimento
- ID duplicati e 'ID documento' impostato su 'ID' generano operazioni di upsert
- ID duplicati e 'ID documento' non impostato generano un errore dopo il primo documento

Se si desidera salvare <i>tutti</i> i documenti inclusi quelli con un ID duplicato, rinominare il campo ID nella query (con la parola chiave AS) e consentire a Cosmos DB di creare il campo ID o sostituire l'ID con un altro valore della colonna (usando la parola chiave AS o tramite l'impostazione di 'ID documento').

## <a name="data-partitioning-in-cosmos-db"></a>Partizionamento dei dati in Cosmos DB
I contenitori [senza limiti](../cosmos-db/partition-data.md) di Azure Cosmos DB costituiscono l'approccio consigliato per il partizionamento dei dati, in quanto Azure Cosmos DB ridimensiona automaticamente le partizioni in base al carico di lavoro. Durante la scrittura in contenitori senza limiti, Analisi di flusso di Azure usa un numero di writer paralleli uguale a quello usato nel passaggio di query precedente o nello schema di partizionamento di input.
> [!NOTE]
> A questo punto, Azure Stream Analitica supporta solo i contenitori senza limiti con chiavi di partizione di primo livello. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate (ad esempio `/region/name`) non sono supportate. 

A seconda della scelta della chiave di partizione potrebbe essere visualizzato ciò _avviso_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

È importante scegliere una proprietà chiave di partizione con un numero di valori distinct, che consente di distribuire il carico di lavoro in modo uniforme tra questi valori. Come un elemento naturale di partizionamento, le richieste che interessa la stessa chiave di partizione sono limitate dalla velocità effettiva massima di una singola partizione. Inoltre, le dimensioni di archiviazione per i documenti che appartengono alla stessa chiave di partizione sono limitata a 10GB. Una chiave di partizione ideale appare spesso come filtro nelle query e ha una cardinalità sufficiente per garantire la scalabilità della soluzione.

Una chiave di partizione è anche il limite per le transazioni in stored procedure e trigger di DocumentDB. È consigliabile scegliere la chiave di partizione in modo che i documenti che si verificano insieme nelle transazioni condividono lo stesso valore di chiave di partizione. L'articolo [partizionamento in Cosmos DB](../cosmos-db/partitioning-overview.md) fornisce altri dettagli sulla scelta di una chiave di partizione.

Per i contenitori di Azure Cosmos DB fissi, Analitica Stream non consente alcuna possibilità di scalare orizzontalmente o verticalmente una volta effettuato completi. Tali raccolte hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR al secondo.  Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore senza limiti, ad esempio con una velocità effettiva di almeno 1000 UR al secondo e una chiave di partizione, è necessario usare l'[utilità di migrazione dati](../cosmos-db/import-data.md) o la [libreria di feed di modifiche](../cosmos-db/change-feed.md).

La possibilità di scrivere in più contenitori fissi verrà deprecata e non è consigliabile per la scalabilità orizzontale il processo di Stream Analitica.

## <a name="improved-throughput-with-compatibility-level-12"></a>Miglioramento della velocità effettiva con 1.2 a livello di compatibilità
Con livello di compatibilità 1.2, Analitica Stream supporta l'integrazione nativa in blocco di scrittura in Cosmos DB. Ciò consente la scrittura in modo efficace a Cosmos DB con l'ottimizzazione della velocità effettiva e in modo efficiente le richieste di limitazione delle richieste di handle. Il meccanismo di scrittura migliorate è disponibile in un nuovo livello di compatibilità a causa di una differenza di comportamento di upsert.  Prima di 1.2, il comportamento di upsert è inserire o unire il documento. 1\.2, viene modificato il comportamento di Upsert per inserire o sostituire il documento. 

Prima di 1.2, utilizza una stored procedure personalizzata per operazioni bulk di documenti upsert per chiave di partizione in Cosmos DB, in cui un batch viene scritto come una transazione. Anche quando un record singolo ha rilevato un errore temporaneo (limitazione), l'intero batch deve essere riprovata. Ciò è reso scenari con limitazione ragionevole anche relativamente più lenti. Successivo confronto viene illustrato come questi processi si comporteranno 1.2.

L'esempio seguente illustra due processi Stream Analitica identici lettura da un input di Hub eventi stesso. Sono entrambi i processi Stream Analitica [completamente partizionata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) con una query pass-through e scrittura ai contenitori di COSMOS DB identici. Le metriche a sinistra sono dal processo configurato con il livello di compatibilità 1.0 e quelle a destra sono configurate con 1.2. Chiave di partizione di un contenitore Cosmos DB è un GUID univoco proveniente dall'evento di input.

![confronto tra le metriche di analitica di flusso](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Frequenza degli eventi in ingresso nell'Hub eventi è superiore a quello dei contenitori Cosmos DB (20K UR) sono configurati per accettare, in modo che la limitazione delle richieste è previsto in Cosmos DB 2x. Il processo con 1.2, tuttavia, in modo coerente scrive con un basso utilizzo medio di % unità di streaming e una velocità effettiva superiore (eventi di Output/minuto). Nell'ambiente in uso, questa differenza dipenderà pochi altri fattori come la scelta del formato di eventi, le dimensioni di messaggi/eventi di input, le chiavi di partizione, query e così via.

![confronto tra le metriche di COSMOS db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2, è più intelligente nell'utilizzo del 100% della velocità effettiva disponibile in Cosmos DB con un numero molto ridotto nuovi invii dalla limitazione di frequenza la limitazione delle richieste/Analitica Stream. Ciò offre un'esperienza migliore per altri carichi di lavoro, ad esempio le query in esecuzione sul contenitore nello stesso momento. Nel caso in cui si vuole provare la modalità ASA viene scalata orizzontalmente con Cosmos DB come sink per k 1 e 10k. i messaggi al secondo, ecco un' [progetto di esempi di azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) che consente di eseguire questa operazione.
Si noti che velocità effettiva di output di Cosmos DB è identica a 1.0 e 1.1. Poiché 1.2 non è attualmente il valore predefinito, è possibile [impostare il livello di compatibilità](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) per un processo di Stream Analitica tramite portale o tramite il [creare processi, chiamare l'API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Dispone *consigliabile* usare 1.2 a livello di compatibilità in ASA con Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Impostazioni di Cosmos DB per l'output JSON

La creazione di Cosmos DB come output nell'analisi di flusso genera una richiesta di informazioni, come illustrato di seguito. Questa sezione fornisce una spiegazione della definizione delle proprietà.

![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrizione|
|-------------   | -------------|
|Alias di output    | Un alias per fare riferimento a questo output nella query ASA.|
|Sottoscrizione    | Scegliere la sottoscrizione di Azure.|
|Account ID      | Nome o URI endpoint dell'account Azure Cosmos DB.|
|Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB.|
|Database        | Nome del database Azure Cosmos DB.|
|Nome contenitore | Il nome del contenitore da usare. `MyContainer` è un esempio valido di input - un contenitore denominato `MyContainer` deve esistere.  |
|Document ID     | facoltativo. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lasciato vuoto, tutti gli eventi verranno inseriti senza alcuna opzione di aggiornamento.|
