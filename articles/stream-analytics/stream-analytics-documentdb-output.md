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
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402314"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
L'analisi di flusso può usare [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Cosmos DB, vedere l'articolo che descrive il [percorso di apprendimento di Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per un’introduzione. 

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta solo la connessione ad Azure Cosmos DB tramite l'**API SQL**.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Nozioni di base di Cosmos DB come destinazione di output
L'output di Azure Cosmos DB nell'analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nelle raccolte di Cosmos DB. Analisi di flusso di Azure non crea raccolte nel database, ma ne richiede la creazione anticipata da parte dell'utente. In questo modo, i costi di fatturazione delle raccolte di Cosmos DB vengono controllati dall'utente ed è possibile ottimizzare direttamente le prestazioni, la coerenza e la capacità delle raccolte usando le [API di Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Di seguito sono descritte alcune delle opzioni per le raccolte di Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
In base ai requisiti dell'applicazione, Azure Cosmos DB ti permette di ottimizzare il database e le raccolte e ottenere un buon compromesso tra coerenza, disponibilità e latenza. A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. Per impostazione predefinita, Azure Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nella raccolta. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di Azure Cosmos DB. Per altre informazioni, rivedere l'articolo relativo a come [modificare i livelli di coerenza del database e delle query](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di Analisi di flusso di Azure con Azure Cosmos DB consente di inserire o aggiornare i record nella raccolta in base a una determinata colonna ID documento. Questa implementazione è detta anche *upsert*.

Analisi di flusso di Azure usa un approccio upsert ottimistico in cui gli aggiornamenti vengono eseguiti solo quando l'inserimento non riesce a causa di un conflitto di ID documento. Con il livello di compatibilità 1.0, questo aggiornamento viene eseguito come una PATCH, consentendo aggiornamenti parziali al documento, vale a dire, l'aggiunta di nuove proprietà o la sostituzione di che una proprietà esistente viene eseguita in modo incrementale. Le modifiche apportate ai valori delle proprietà di matrice nel documento JSON comportano, tuttavia, la sovrascrittura dell'intera matrice, ovvero non viene eseguito il merge della matrice. 1.2, per inserire o sostituire il documento viene modificato il comportamento di upsert. Questo è descritto più dettagliatamente nella sezione 1.2 a livello di compatibilità di seguito.

Se il documento JSON in ingresso include già un campo ID esistente, tale campo verrà usato automaticamente come colonna ID documento in Cosmos DB e le eventuali scritture successive verranno gestite come tali. Questo scenario sarà caratterizzato dalle situazioni seguenti:
- gli ID univoci generano operazioni di inserimento
- ID duplicati e 'ID documento' impostato su 'ID' generano operazioni di upsert
- ID duplicati e 'ID documento' non impostato generano un errore dopo il primo documento

Se si desidera salvare <i>tutti</i> i documenti inclusi quelli con un ID duplicato, rinominare il campo ID nella query (con la parola chiave AS) e consentire a Cosmos DB di creare il campo ID o sostituire l'ID con un altro valore della colonna (usando la parola chiave AS o tramite l'impostazione di 'ID documento').

## <a name="data-partitioning-in-cosmos-db"></a>Partizionamento dei dati in Cosmos DB
I contenitori [senza limiti](../cosmos-db/partition-data.md) di Azure Cosmos DB costituiscono l'approccio consigliato per il partizionamento dei dati, in quanto Azure Cosmos DB ridimensiona automaticamente le partizioni in base al carico di lavoro. Durante la scrittura in contenitori senza limiti, Analisi di flusso di Azure usa un numero di writer paralleli uguale a quello usato nel passaggio di query precedente o nello schema di partizionamento di input.
> [!Note]
> A questo punto, Analisi di flusso di Azure supporta solo un numero illimitato di raccolte con chiavi di partizione di primo livello. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate (ad esempio `/region/name`) non sono supportate. 

Per le raccolte di Azure Cosmos DB fisse, Analisi di flusso di Azure non consente alcuna possibilità di ridimensionamento se le raccolte sono al completo. Tali raccolte hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR al secondo.  Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore senza limiti, ad esempio con una velocità effettiva di almeno 1000 UR al secondo e una chiave di partizione, è necessario usare l'[utilità di migrazione dati](../cosmos-db/import-data.md) o la [libreria di feed di modifiche](../cosmos-db/change-feed.md).

La scrittura in più contenitori fissi verrà deprecata e non costituisce l'approccio consigliato per il ridimensionamento del processo di Analisi di flusso di Azure. Per altre informazioni, vedere [Partizionamento e scalabilità in Cosmos DB](../cosmos-db/sql-api-partition-data.md).

## <a name="improved-throughput-with-compatibility-level-12"></a>Miglioramento della velocità effettiva con 1.2 a livello di compatibilità
Con livello di compatibilità 1.2, Analitica Stream supporta l'integrazione nativa in blocco di scrittura in Cosmos DB. Ciò consente la scrittura in modo efficace a Cosmos DB con l'ottimizzazione della velocità effettiva e in modo efficiente le richieste di limitazione delle richieste di handle. Il meccanismo di scrittura migliorate è disponibile in un nuovo livello di compatibilità a causa di una differenza di comportamento di upsert.  Prima di 1.2, il comportamento di upsert è inserire o unire il documento. 1.2, viene modificato il comportamento di Upsert per inserire o sostituire il documento. 

Prima di 1.2, utilizza una stored procedure personalizzata per operazioni bulk di documenti upsert per chiave di partizione in Cosmos DB, in cui un batch viene scritto come una transazione. Anche quando un record singolo ha rilevato un errore temporaneo (limitazione), l'intero batch deve essere riprovata. Ciò è reso scenari con limitazione ragionevole anche relativamente più lenti. Successivo confronto viene illustrato come questi processi si comporteranno 1.2.

Sotto il programma di installazione mostra due processi Stream Analitica identici la lettura da stesso input (hub eventi). Sono entrambi i processi Stream Analitica [completamente partizionata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) con una query pass-through e la scrittura identiche nelle raccolte di COSMOS DB. La metrica a sinistra è il processo configurato con il livello di compatibilità 1.0 e quelli sulla destra è configurato con 1.2. Chiave di partizione di raccolte di COSMOS DB è un guid univoco proveniente dall'evento di input.

![confronto tra le metriche di analitica di flusso](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Frequenza degli eventi in ingresso nell'Hub eventi è superiore a raccolte di Cosmos DB (20K UR) sono configurate per accettare, in modo che la limitazione delle richieste è previsto in Cosmos DB 2x. Il processo con 1.2, tuttavia, in modo coerente scrive con un basso utilizzo medio di % unità di streaming e una velocità effettiva superiore (eventi di Output/minuto). Nell'ambiente in uso, questa differenza dipenderà pochi altri fattori come la scelta del formato di eventi, le dimensioni di messaggi/eventi di input, le chiavi di partizione, query e così via.

![confronto tra le metriche di COSMOS db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1.2, è più intelligente nell'utilizzo del 100% della velocità effettiva disponibile in Cosmos DB con un numero molto ridotto nuovi invii dalla limitazione di frequenza la limitazione delle richieste/Analitica Stream. Ciò offre un'esperienza migliore per altri carichi di lavoro, ad esempio le query in esecuzione per la raccolta nello stesso momento. Nel caso in cui si vuole provare la modalità ASA viene scalata orizzontalmente con Cosmos DB come sink per k 1 e 10k. i messaggi al secondo, ecco un' [progetto di esempi di azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) che consente di eseguire questa operazione.
Si noti che velocità effettiva di output di Cosmos DB è identica a 1.0 e 1.1. Poiché 1.2 non è attualmente il valore predefinito, è possibile [impostare il livello di compatibilità](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) per un processo di Stream Analitica tramite portale o tramite il [creare processi, chiamare l'API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Dispone *consigliabile* usare 1.2 a livello di compatibilità in ASA con Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Impostazioni di Cosmos DB per l'output JSON

La creazione di Cosmos DB come output nell'analisi di flusso genera una richiesta di informazioni, come illustrato di seguito. Questa sezione fornisce una spiegazione della definizione delle proprietà.

![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | DESCRIZIONE|
|-------------   | -------------|
|Alias di output    | Un alias per fare riferimento a questo output nella query ASA.|
|Sottoscrizione    | Scegliere la sottoscrizione di Azure.|
|Account ID      | Nome o URI endpoint dell'account Azure Cosmos DB.|
|Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB.|
|Database        | Nome del database Azure Cosmos DB.|
|Modello nome raccolta | Nome di raccolta per le raccolte da usare. `MyCollection` è un esempio di input valido. Una raccolta denominata `MyCollection` deve essere presente.  |
|Document ID     | facoltativo. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lasciato vuoto, tutti gli eventi verranno inseriti senza alcuna opzione di aggiornamento.|
