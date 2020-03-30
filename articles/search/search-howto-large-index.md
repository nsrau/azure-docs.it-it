---
title: Indicizzare set di dati di grandi dimensioni usando gli indicizzatori incorporatiIndex large data set using built-in indexers
titleSuffix: Azure Cognitive Search
description: Strategie per l'indicizzazione di dati di grandi dimensioni o l'indicizzazione a elevato utilizzo di errori computazionali tramite la modalità batch, le risorse e le tecniche per l'indicizzazione pianificata, parallela e distribuita.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190969"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Come indicizzare set di dati di grandi dimensioni in Ricerca cognitiva di AzureHow to index large data sets in Azure Cognitive Search

Man mano che i volumi di dati aumentano o le esigenze di elaborazione cambiano, è possibile che le strategie di indicizzazione semplici o predefinite non siano più pratiche. Per Ricerca cognitiva di Azure, sono disponibili diversi approcci per ospitare set di dati più grandi, che vanno dalla struttura di una richiesta di caricamento dei dati all'uso di un indicizzatore specifico dell'origine per carichi di lavoro pianificati e distribuiti.

Le stesse tecniche si applicano anche ai processi a esecuzione prolungata. In particolare, i passaggi descritti [nell'indicizzazione parallela](#parallel-indexing) sono utili per l'indicizzazione intensiva dal punto di vista computazionale, ad esempio l'analisi delle immagini o l'elaborazione del linguaggio naturale in una pipeline di [arricchimento Dell'ia-A.](cognitive-search-concept-intro.md)

Nelle sezioni seguenti vengono esaminate tre tecniche per l'indicizzazione di grandi quantità di dati.

## <a name="option-1-pass-multiple-documents"></a>Opzione 1: Passare più documentiOption 1: Pass multiple documents

Uno dei meccanismi più semplici per l'indicizzazione di set di dati di grandi dimensioni consiste nell'inviare più documenti o record in un'unica richiesta. Se le dimensioni dell'intero payload sono inferiori a 16 MB, una richiesta può gestire fino a 1000 documenti in un'operazione di caricamento in blocco. Questi limiti si applicano se si utilizza [l'API REST Aggiungi documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) o il metodo [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) in .NET SDK. Per entrambe le API, è necessario creare un pacchetto di 1000 documenti nel corpo di ogni richiesta.

L'indicizzazione batch viene implementata per le singole richieste tramite REST o .NET o mediante gli indicizzatori. Alcuni indicizzatori funzionano con limitazioni diverse. In particolare, l'indicizzazione BLOB di Azure limita le dimensioni dei batch a 10 documenti in considerazione delle dimensioni medie superiori dei documenti. Per gli indicizzatori basati sull'[API REST di creazione indicizzatore](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer), è possibile impostare l'argomento `BatchSize` per personalizzare questa impostazione in base alle caratteristiche dei dati. 

> [!NOTE]
> Per ridurre le dimensioni del documento, evitare di aggiungere dati non queryable a un indice. Le immagini e altri dati binari non sono direttamente disponibili per la ricerca e non devono quindi essere archiviati nell'indice. Per integrare i dati non disponibili per query nei risultati della ricerca, occorre definire un campo non disponibile per la ricerca che archivia un riferimento URL alla risorsa.

## <a name="option-2-add-resources"></a>Opzione 2: Aggiungere risorseOption 2: Add resources

I servizi di cui viene effettuato il provisioning a uno dei [piani tariffari Standard](search-sku-tier.md) hanno spesso una capacità sottoutilizzata sia per l'archiviazione che per i carichi di lavoro (query o indicizzazione), motivo per cui l'[aumento del numero di partizioni e repliche](search-capacity-planning.md) rappresenta la soluzione più ovvia per gestire set di dati di grandi dimensioni. Per ottenere risultati ottimali sono necessarie entrambe le risorse: le partizioni per l'archiviazione e le repliche per l'inserimento dati.

L'aumento delle repliche e delle partizioni è costituito da eventi fatturabili che aumentano il costo, ma a meno che non si indicizzi continuamente sotto il carico massimo, è possibile aggiungere la scala per la durata del processo di indicizzazione e quindi regolare i livelli delle risorse verso il basso dopo l'indicizzazione Finito.

## <a name="option-3-use-indexers"></a>Opzione 3: utilizzare gli indicizzatoriOption 3: Use indexers

[Gli indicizzatori](search-indexer-overview.md) vengono usati per eseguire la ricerca per indicizzazione delle origini dati di Azure supportate per il contenuto ricercabile. Sebbene non siano appositamente destinati all'indicizzazione su larga scala, diverse funzionalità degli indicizzatori sono particolarmente utili in presenza di set di dati di grandi dimensioni:

+ Le utilità di pianificazione consentono di suddividere l'indicizzazione in intervalli regolari in modo da distribuirne l'esecuzione nel tempo.
+ L'indicizzazione pianificata può ripartire dall'ultimo punto di arresto noto. Se una ricerca per indicizzazione su un'origine dati non viene completata in 24 ore, l'indicizzatore riprenderà l'indicizzazione il secondo giorno a partire dal punto di interruzione.
+ Il partizionamento dei dati in singole origini dati di dimensioni inferiori consente l'elaborazione parallela. È possibile suddividere i dati di origine in componenti più piccoli, ad esempio in più contenitori nell'archiviazione BLOB di Azure, e quindi creare oggetti [origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) corrispondenti in Ricerca cognitiva di Azure che possono essere indicizzati in parallelo.

> [!NOTE]
> Gli indicizzatori sono specifici dell'origine dati, pertanto l'utilizzo di un approccio all'indicizzatore è possibile solo per origini dati selezionate in Azure: [Database SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Archiviazione BLOB](search-howto-indexing-azure-blob-storage.md), Archiviazione [tabelle](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Indicizzazione pianificata

La pianificazione degli indicizzatori è un meccanismo importante per l'elaborazione di grandi set di dati, così come per i processi a esecuzione lenta come l'analisi delle immagini in una pipeline di ricerca cognitiva. L'elaborazione degli indicizzatori funziona entro una finestra di 24 ore. Se l'elaborazione non viene completata nell'arco di 24 ore, i comportamenti della pianificazione dell'indicizzatore possono essere usati a proprio vantaggio. 

Per impostazione predefinita, l'indicizzazione pianificata viene avviata a intervalli specifici, in cui un processo viene in genere completato prima di riprendere nell'intervallo pianificato successivo. Tuttavia, se l'elaborazione non viene completata entro l'intervallo, l'indicizzatore si arresta perché il tempo a disposizione è terminato. All'intervallo successivo l'elaborazione riprende da dove si è interrotta perché il sistema tiene traccia del punto in cui si è verificata l'interruzione. 

In pratica per i caricamenti di indici che richiedono più giorni, è possibile inserire l'indicizzatore in una pianificazione di 24 ore. Quando l'indicizzazione riprende per il ciclo di 24 ore successivo, riparte dall'ultimo documento valido noto. In questo modo un indicizzatore può continuare a funzionare tramite un backlog del documento per più giorni fino a quando non vengono elaborati tutti i documenti non elaborati. Per altre informazioni su questo approccio, vedere [Indicizzazione di set di dati di grandi dimensioni](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Per altre informazioni sull'impostazione delle pianificazioni in generale, vedere [Creare l'API REST dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) o vedere [Come pianificare gli indicizzatori per Ricerca cognitiva](search-howto-schedule-indexers.md)di Azure.For more information about setting schedules in general, see Create indexer REST API or see How to schedule indexers for Azure Cognitive Search .

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indicizzazione parallela

Una strategia di indicizzazione parallela si basa sull'indicizzazione di più origini dati in contemporanea, dove ogni definizione di origine dati specifica un subset dei dati. 

Per requisiti di indicizzazione non comuni e con elevati livelli di calcolo, ad esempio OCR su documenti digitalizzati in una pipeline di ricerca cognitiva, analisi delle immagini o elaborazione del linguaggio naturale, una strategia di indicizzazione parallela è spesso l'approccio giusto per completare un processo a esecuzione prolungata nel più breve tempo possibile. Se è possibile eliminare o ridurre le richieste di query, l'indicizzazione parallela in un servizio che non gestisce contemporaneamente le query rappresenta la strategia migliore per gestire grandi contenuti a elaborazione lenta. 

L'elaborazione parallela prevede le operazioni seguenti:

+ Suddividere i dati di origine in più contenitori o in più cartelle virtuali all'interno dello stesso contenitore. 
+ Eseguire il mapping di ogni mini set di dati alla propria [origine dati,](https://docs.microsoft.com/rest/api/searchservice/create-data-source)associati al proprio [indicizzatore.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ Per la ricerca cognitiva, fare riferimento allo stesso [set di competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) in ogni definizione dell'indicizzatore.
+ Scrivere nello stesso indice di ricerca di destinazione. 
+ Pianificare l'esecuzione di tutti gli indicizzatori nello stesso momento.

> [!NOTE]
> In Ricerca cognitiva di Azure non è possibile assegnare singole repliche o partizioni all'indicizzazione o all'elaborazione delle query. Il sistema determina la modalità di utilizzo delle risorse. Per comprendere l'impatto sulle prestazioni delle query, è possibile provare l'indicizzazione parallela in un ambiente di test prima di eseguirne il rollup nell'ambiente di produzione.  

### <a name="how-to-configure-parallel-indexing"></a>Come configurare l'indicizzazione parallela

Per gli indicizzatori la capacità di elaborazione si basa vagamente su un sottosistema dell'indicizzatore per ogni unità di servizio usata dal servizio di ricerca. Nei servizi Ricerca cognitiva di Azure di cui è stato eseguito il provisioning sono possibili più indicizzatori simultanei nei livelli Basic o Standard con almeno due repliche. 

1. Nella pagina **Panoramica** della dashboard del servizio di ricerca del [portale di Azure](https://portal.azure.com) controllare il **Piano tariffario** per confermarne la possibilità di gestione dell'indicizzazione parallela. I livelli Basic e Standard offrono più repliche.

2. In**Scala** **impostazioni** >  [aumentare le repliche](search-capacity-planning.md) per l'elaborazione parallela: una replica aggiuntiva per ogni carico di lavoro dell'indicizzatore. Indicare un numero sufficiente per il volume di query esistente. Sacrificare i carichi di lavoro delle query per l'indicizzazione non è un buon compromesso.

3. Distribuire i dati in più contenitori a un livello che gli indicizzatori di Ricerca cognitiva di Azure possono raggiungere. Potrebbe trattarsi di tabelle multiple nel database SQL di Azure, contenitori multipli nel servizio Archiviazione BLOB di Azure o di raccolte multiple. Definire un oggetto di origine dati per ogni tabella o contenitore.

4. Creare e pianificare indicizzatori multipli da eseguire in parallelo:

   + Si supponga di avere un servizio con sei repliche. Configurare i sei indicizzatori, ognuno dei quali mappato in un'origine dati contenente un sesto del set di dati per una divisione in sei parti dell'intero set di dati. 

   + Puntare ogni indicizzatore allo stesso indice. Per i carichi di lavoro di ricerca cognitivi puntare ogni indicizzatore allo stesso set competenze.

   + In ogni definizione dell'indicizzatore pianificare lo stesso modello della fase di esecuzione. Ad esempio, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` crea una pianificazione il 15-05-2018 in tutti gli indicizzatori, con un'esecuzione a intervalli di otto ore.

All'ora pianificata tutti gli indicizzatori avviano l'esecuzione, caricando i dati, applicando gli arricchimenti, se è stata configurata una pipeline di ricerca cognitiva, e avviando operazioni di scrittura nell'indice. Ricerca cognitiva di Azure non blocca l'indice per gli aggiornamenti. Vengono gestite le scritture simultanee, con un nuovo tentativo se un'operazione di scrittura specifica non riesce al primo tentativo.

> [!Note]
> Quando si aumentano le repliche, è opportuno aumentare il conteggio delle partizioni se le dimensioni dell'indice sono state pensate per un aumento significativo. Le partizioni archiviano sezioni di contenuto indicizzato: più sono le partizioni, più piccola è la sezione che ognuna deve archiviare.

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di Archiviazione BLOB di AzureAzure Blob Storage indexer](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di Archiviazione tabelle di AzureAzure Table Storage indexer](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Ricerca cognitiva di AzureSecurity in Azure Cognitive Search](search-security-overview.md)
