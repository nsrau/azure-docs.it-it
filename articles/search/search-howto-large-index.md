---
title: Indicizzare set di dati di grandi dimensioni con indicizzatori predefiniti
titleSuffix: Azure Cognitive Search
description: Strategie per l'indicizzazione di dati di grandi dimensioni o l'indicizzazione a elevato utilizzo di calcolo tramite modalità batch, riapprovvigionamento e tecniche per l'indicizzazione pianificata, parallela e distribuita.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 80307c97464e61d7b7d338703de90d1199adc819
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927018"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Come indicizzare set di dati di grandi dimensioni in ricerca cognitiva di Azure

Ricerca cognitiva di Azure supporta [due approcci di base](search-what-is-data-import.md) per l'importazione di dati in un indice di ricerca: eseguire il *push* dei dati nell'indice a livello di codice oppure puntare a un [indicizzatore di Ricerca cognitiva di Azure](search-indexer-overview.md) in un'origine dati supportata per eseguire il *pull* nei dati.

Man mano che i volumi di dati crescono o si modificano le esigenze, è possibile che le strategie di indicizzazione semplici o predefinite non siano più pratiche. Per ricerca cognitiva di Azure, esistono diversi approcci per ospitare set di dati di dimensioni maggiori, da come si struttura una richiesta di caricamento dei dati, usando un indicizzatore specifico dell'origine per i carichi di lavoro pianificati e distribuiti.

Le stesse tecniche si applicano anche ai processi con esecuzione prolungata. In particolare, i passaggi descritti nell' [indicizzazione parallela](#parallel-indexing) sono utili per l'indicizzazione a elevato utilizzo di calcolo, ad esempio l'analisi delle immagini o l'elaborazione del linguaggio naturale in una [pipeline di arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md).

Le sezioni seguenti illustrano le tecniche per l'indicizzazione di grandi quantità di dati tramite l'API push e gli indicizzatori.

## <a name="push-api"></a>API Push

Quando si esegue il push dei dati in un indice, esistono diverse considerazioni chiave che influiscano sulle velocità di indicizzazione per l'API push. Questi fattori sono descritti nella sezione seguente. 

Oltre alle informazioni contenute in questo articolo, è anche possibile sfruttare i vantaggi degli esempi di codice nell'esercitazione relativa all' [ottimizzazione delle velocità di indicizzazione](tutorial-optimize-indexing-push-api.md) per altre informazioni.

### <a name="service-tier-and-number-of-partitionsreplicas"></a>Livello di servizio e numero di partizioni/repliche

L'aggiunta di partizioni o l'aumento del livello del servizio di ricerca aumenteranno le velocità di indicizzazione.

L'aggiunta di repliche aggiuntive può aumentare anche la velocità di indicizzazione, ma non è garantita. D'altra parte, le repliche aggiuntive aumenteranno il volume di query che il servizio di ricerca è in grado di gestire. Le repliche sono anche un componente chiave per ottenere un [contratto di contratto](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Prima di aggiungere partizioni o repliche o eseguire l'aggiornamento a un livello superiore, prendere in considerazione il costo monetario e il tempo di allocazione. L'aggiunta di partizioni può aumentare significativamente la velocità di indicizzazione, ma l'aggiunta o la rimozione può richiedere da 15 minuti a diverse ore. Per ulteriori informazioni, vedere la documentazione relativa alla [regolazione della capacità](search-capacity-planning.md).

### <a name="index-schema"></a>Schema dell'indice

Lo schema dell'indice svolge un ruolo importante nell'indicizzazione dei dati. L'aggiunta di campi e l'aggiunta di proprietà aggiuntive a tali campi, ad esempio *ricercabile*, con *facet*o *filtrabile*, consentono di ridurre le velocità di indicizzazione.

In generale, è consigliabile aggiungere proprietà aggiuntive ai campi solo se si intende usarle.

> [!NOTE]
> Per ridurre le dimensioni del documento, evitare di aggiungere dati non Queryable a un indice. Le immagini e altri dati binari non sono direttamente disponibili per la ricerca e non devono quindi essere archiviati nell'indice. Per integrare i dati non disponibili per query nei risultati della ricerca, occorre definire un campo non disponibile per la ricerca che archivia un riferimento URL alla risorsa.

### <a name="batch-size"></a>Dimensioni batch

Uno dei meccanismi più semplici per l'indicizzazione di set di dati di grandi dimensioni consiste nell'inviare più documenti o record in un'unica richiesta. Se le dimensioni dell'intero payload sono inferiori a 16 MB, una richiesta può gestire fino a 1000 documenti in un'operazione di caricamento in blocco. Questi limiti si applicano se si usa l' [API REST di Add Documents](/rest/api/searchservice/addupdate-or-delete-documents) o il [metodo index](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) in .NET SDK. Per entrambe le API, è necessario creare il pacchetto di 1000 documenti nel corpo di ogni richiesta.

L'utilizzo di batch per indicizzare i documenti consente di migliorare significativamente le prestazioni di indicizzazione. Determinare le dimensioni ottimali dei batch per i dati è fondamentale per ottimizzare la velocità di indicizzazione. I due fattori principali che influiscono sulle dimensioni ottimali dei batch sono i seguenti:
+ Schema dell'indice
+ Dimensioni dei dati

Poiché le dimensioni ottimali del batch dipendono dall'indice e dai dati, l'approccio migliore consiste nel testare dimensioni batch diverse per determinare quali sono i risultati della velocità di indicizzazione più veloce per lo scenario. Questa [esercitazione](tutorial-optimize-indexing-push-api.md) fornisce il codice di esempio per testare le dimensioni dei batch usando .NET SDK. 

### <a name="number-of-threadsworkers"></a>Numero di thread/ruoli di lavoro

Per sfruttare appieno la velocità di indicizzazione di Ricerca cognitiva di Azure, sarà probabilmente necessario usare più thread per inviare simultaneamente richieste di indicizzazione in batch al servizio.  

Il numero ottimale di thread è determinato da:

+ Il livello del servizio di ricerca
+ Numero di partizioni
+ Dimensioni dei batch
+ Schema dell'indice

È possibile modificare questo esempio ed eseguire test con diversi conteggi dei thread per determinare il conteggio ottimale per lo scenario specifico. Con l'esecuzione simultanea di diversi thread, comunque, dovrebbe essere possibile sfruttare la maggior parte dei vantaggi in termini di efficienza. 

> [!NOTE]
> Aumentando il livello del servizio di ricerca o aumentando le partizioni, è necessario aumentare anche il numero di thread simultanei.

Quando si aumentano le richieste che raggiungono il servizio di ricerca, potrebbero essere visualizzati [codici di stato HTTP](/rest/api/searchservice/http-status-codes) che indicano che la richiesta non è stata interamente completata. Durante l'indicizzazione, i due codici di stato HTTP comuni sono i seguenti:

+ **503 - Servizio non disponibile**. Questo errore indica che il sistema è in sovraccarico e al momento la richiesta non può essere elaborata.
+ **207 - Multi-Status**. Questo errore indica che alcuni documenti hanno avuto esito positivo, ma almeno uno ha avuto esito negativo.

### <a name="retry-strategy"></a>Strategia di ripetizione dei tentativi 

Se si verifica un errore, le richieste dovranno essere ripetute usando una [strategia di ripetizione dei tentativi con backoff esponenziale](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

.NET SDK di Ricerca cognitiva di Azure ripete automaticamente le richieste con codice 503 e le altre richieste non riuscite, ma per ripetere le richieste con codice 207 è necessario implementare una logica personalizzata. È anche possibile usare strumenti open source come [Polly](https://github.com/App-vNext/Polly) per implementare una strategia di ripetizione dei tentativi.

### <a name="network-data-transfer-speeds"></a>Velocità di trasferimento dei dati di rete

La velocità di trasferimento dei dati di rete può essere un fattore di limitazione durante l'indicizzazione dei dati. L'indicizzazione dei dati dall'interno dell'ambiente Azure è un modo semplice per velocizzare l'indicizzazione.

## <a name="indexers"></a>Indicizzatori

Gli [indicizzatori](search-indexer-overview.md) vengono usati per eseguire la ricerca per indicizzazione delle origini dati di Azure supportate. Sebbene non siano appositamente destinati all'indicizzazione su larga scala, diverse funzionalità degli indicizzatori sono particolarmente utili in presenza di set di dati di grandi dimensioni:

+ Le utilità di pianificazione consentono di suddividere l'indicizzazione in intervalli regolari in modo da distribuirne l'esecuzione nel tempo.
+ L'indicizzazione pianificata può ripartire dall'ultimo punto di arresto noto. Se una ricerca per indicizzazione su un'origine dati non viene completata in 24 ore, l'indicizzatore riprenderà l'indicizzazione il secondo giorno a partire dal punto di interruzione.
+ Il partizionamento dei dati in singole origini dati di dimensioni inferiori consente l'elaborazione parallela. È possibile suddividere i dati di origine in componenti più piccoli, ad esempio in più contenitori nell'archivio BLOB di Azure, e quindi creare più [oggetti origine dati](/rest/api/searchservice/create-data-source) corrispondenti in Azure ricerca cognitiva che possono essere indicizzati in parallelo.

> [!NOTE]
> Gli indicizzatori sono specifici dell'origine dati, pertanto l'uso di un approccio indicizzatore è possibile solo per le origini dati selezionate in Azure: [database SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [archiviazione BLOB](search-howto-indexing-azure-blob-storage.md), [archiviazione tabelle](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="batch-size"></a>Dimensioni batch

Come per l'API push, gli indicizzatori consentono di configurare il numero di elementi per batch. Per gli indicizzatori basati sull'[API REST di creazione indicizzatore](/rest/api/searchservice/Create-Indexer), è possibile impostare l'argomento `batchSize` per personalizzare questa impostazione in base alle caratteristiche dei dati. 

Le dimensioni di batch predefinite sono specifiche dell'origine dati. Il database SQL di Azure e il Azure Cosmos DB hanno una dimensione di batch predefinita pari a 1000. L'indicizzazione BLOB di Azure, invece, imposta le dimensioni del batch su 10 documenti in riconoscimento della dimensione media del documento più grande. 

### <a name="scheduled-indexing"></a>Indicizzazione pianificata

La pianificazione degli indicizzatori è un meccanismo importante per l'elaborazione di grandi set di dati, così come per i processi a esecuzione lenta come l'analisi delle immagini in una pipeline di ricerca cognitiva. L'elaborazione degli indicizzatori funziona entro una finestra di 24 ore. Se l'elaborazione non viene completata nell'arco di 24 ore, i comportamenti della pianificazione dell'indicizzatore possono essere usati a proprio vantaggio. 

Per impostazione predefinita, l'indicizzazione pianificata viene avviata a intervalli specifici, in cui un processo viene in genere completato prima di riprendere nell'intervallo pianificato successivo. Tuttavia, se l'elaborazione non viene completata entro l'intervallo, l'indicizzatore si arresta perché il tempo a disposizione è terminato. All'intervallo successivo l'elaborazione riprende da dove si è interrotta perché il sistema tiene traccia del punto in cui si è verificata l'interruzione. 

In pratica per i caricamenti di indici che richiedono più giorni, è possibile inserire l'indicizzatore in una pianificazione di 24 ore. Quando l'indicizzazione riprende per il ciclo di 24 ore successivo, riparte dall'ultimo documento valido noto. In questo modo un indicizzatore può continuare a funzionare tramite un backlog del documento per più giorni fino a quando non vengono elaborati tutti i documenti non elaborati. Per altre informazioni su questo approccio, vedere [Indicizzazione di set di dati di grandi dimensioni](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Per altre informazioni sull'impostazione delle pianificazioni in generale, vedere [creare un'API REST dell'indicizzatore](/rest/api/searchservice/Create-Indexer) o vedere [come pianificare gli indicizzatori per ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indicizzazione parallela

Una strategia di indicizzazione parallela si basa sull'indicizzazione di più origini dati in contemporanea, dove ogni definizione di origine dati specifica un subset dei dati. 

Per requisiti di indicizzazione non comuni e con elevati livelli di calcolo, ad esempio OCR su documenti digitalizzati in una pipeline di ricerca cognitiva, analisi delle immagini o elaborazione del linguaggio naturale, una strategia di indicizzazione parallela è spesso l'approccio giusto per completare un processo a esecuzione prolungata nel più breve tempo possibile. Se è possibile eliminare o ridurre le richieste di query, l'indicizzazione parallela in un servizio che non gestisce contemporaneamente le query rappresenta la strategia migliore per gestire grandi contenuti a elaborazione lenta. 

L'elaborazione parallela prevede le operazioni seguenti:

+ Suddividere i dati di origine in più contenitori o in più cartelle virtuali all'interno dello stesso contenitore. 
+ Eseguire il mapping di ogni mini set di dati alla propria [origine dati](/rest/api/searchservice/create-data-source), abbinato al proprio [indicizzatore](/rest/api/searchservice/create-indexer).
+ Per la ricerca cognitiva, fare riferimento allo stesso [set di competenze](/rest/api/searchservice/create-skillset) in ogni definizione dell'indicizzatore.
+ Scrivere nello stesso indice di ricerca di destinazione. 
+ Pianificare l'esecuzione di tutti gli indicizzatori nello stesso momento.

> [!NOTE]
> In ricerca cognitiva di Azure non è possibile assegnare singole repliche o partizioni per l'indicizzazione o l'elaborazione delle query. Il sistema determina la modalità di utilizzo delle risorse. Per comprendere l'impatto sulle prestazioni delle query, è possibile provare a eseguire l'indicizzazione parallela in un ambiente di test prima di eseguirne il rollforward in produzione.  

### <a name="how-to-configure-parallel-indexing"></a>Come configurare l'indicizzazione parallela

Per gli indicizzatori la capacità di elaborazione si basa vagamente su un sottosistema dell'indicizzatore per ogni unità di servizio usata dal servizio di ricerca. Sono possibili più indicizzatori simultanei nei servizi di ricerca cognitiva di Azure di cui è stato effettuato il provisioning nei livelli Basic o standard con almeno due repliche. 

1. Nella pagina **Panoramica** della dashboard del servizio di ricerca del [portale di Azure](https://portal.azure.com) controllare il **Piano tariffario** per confermarne la possibilità di gestione dell'indicizzazione parallela. I livelli Basic e Standard offrono più repliche.

2. È possibile eseguire tutti gli indicizzatori in parallelo come numero di unità di ricerca nel servizio. In **Impostazioni**  >  **ridimensionare** [aumentare le repliche](search-capacity-planning.md) o le partizioni per l'elaborazione parallela: una replica o una partizione aggiuntiva per ogni carico di lavoro dell'indicizzatore. Indicare un numero sufficiente per il volume di query esistente. Sacrificare i carichi di lavoro delle query per l'indicizzazione non è un buon compromesso.

3. Distribuire i dati in più contenitori a un livello che può raggiungere gli indicizzatori di Azure ricerca cognitiva. Potrebbe trattarsi di tabelle multiple nel database SQL di Azure, contenitori multipli nel servizio Archiviazione BLOB di Azure o di raccolte multiple. Definire un oggetto di origine dati per ogni tabella o contenitore.

4. Creare e pianificare indicizzatori multipli da eseguire in parallelo:

   + Si supponga di avere un servizio con sei repliche. Configurare i sei indicizzatori, ognuno dei quali mappato in un'origine dati contenente un sesto del set di dati per una divisione in sei parti dell'intero set di dati. 

   + Puntare ogni indicizzatore allo stesso indice. Per i carichi di lavoro di ricerca cognitivi puntare ogni indicizzatore allo stesso set competenze.

   + In ogni definizione dell'indicizzatore pianificare lo stesso modello della fase di esecuzione. Ad esempio, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` crea una pianificazione il 15-05-2018 in tutti gli indicizzatori, con un'esecuzione a intervalli di otto ore.

All'ora pianificata tutti gli indicizzatori avviano l'esecuzione, caricando i dati, applicando gli arricchimenti, se è stata configurata una pipeline di ricerca cognitiva, e avviando operazioni di scrittura nell'indice. Azure ricerca cognitiva non blocca l'indice per gli aggiornamenti. Vengono gestite le scritture simultanee, con un nuovo tentativo se un'operazione di scrittura specifica non riesce al primo tentativo.

> [!Note]
> Quando si aumentano le repliche, è opportuno aumentare il conteggio delle partizioni se le dimensioni dell'indice sono state pensate per un aumento significativo. Le partizioni archiviano sezioni di contenuto indicizzato: più sono le partizioni, più piccola è la sezione che ognuna deve archiviare.

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Azure ricerca cognitiva](search-security-overview.md)