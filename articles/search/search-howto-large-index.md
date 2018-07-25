---
title: Ricompilare un indice di Ricerca di Azure o aggiornare il contenuto ricercabile | Microsoft Docs
description: Aggiungere nuovi elementi, aggiornare gli elementi o i documenti esistenti o eliminare i documenti obsoleti in una ricompilazione completa o in un'indicizzazione incrementale parziale per aggiornare un indice di Ricerca di Azure.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 0dd7a5d5159144c6b1a050ff4c0443b181976738
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124955"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Come ridimensionare l'indicizzazione in Ricerca di Azure

Man mano che il volume dei dati aumenta o l'elaborazione richiede modifiche, è possibile che le [ricompilazioni e i processi di reindicizzazione](search-howto-reindex.md) non risultino così semplici. 

Come primo passo per soddisfare le richieste, si consiglia di aumentare la [scalabilità e la capacità](search-capacity-planning.md) entro i limiti del servizio esistente. 

Un secondo passaggio, se è possibile usare gli [indicizzatori](search-indexer-overview.md), aggiunge i meccanismi per l'indicizzazione scalabile. Gli indicizzatori sono dotati di un'utilità di pianificazione predefinita che consente di suddividere l'indicizzazione in intervalli regolari o di estendere l'elaborazione oltre la finestra di 24 ore. Per di più, quando abbinati alle definizioni delle origini dati, gli indicizzatori consentono di ottenere un modulo di parallelismo mediante il partizionamento dei dati e l'uso delle pianificazioni da eseguire in parallelo.

### <a name="scheduled-indexing-for-large-data-sets"></a>Indicizzazione pianificata per grandi set di dati

La pianificazione è un meccanismo importante per l'elaborazione di grandi set di dati e le analisi a esecuzione lenta, ad esempio, analisi delle immagini in una pipeline di ricerca cognitiva. L'elaborazione degli indicizzatori funziona entro una finestra di 24 ore. Se l'elaborazione non viene completata nell'arco di 24 ore, i comportamenti della pianificazione dell'indicizzatore possono essere usati a proprio vantaggio. 

Per impostazione predefinita, l'indicizzazione pianificata viene avviata a intervalli specifici, in cui un processo viene in genere completato prima di riprendere nell'intervallo pianificato successivo. Tuttavia, se l'elaborazione non viene completata entro l'intervallo, l'indicizzatore si arresta perché il tempo a disposizione è terminato. All'intervallo successivo l'elaborazione riprende da dove si è interrotta perché il sistema tiene traccia del punto in cui si è verificata l'interruzione. 

In pratica per i caricamenti di indici che richiedono più giorni, è possibile inserire l'indicizzatore in una pianificazione di 24 ore. Quando l'indicizzazione riprende nelle successive 24 ore, riavvia l'ultimo documento valido e noto. In questo modo un indicizzatore può continuare a funzionare tramite un backlog del documento per più giorni fino a quando non vengono elaborati tutti i documenti non elaborati. Per altre informazioni su questo approccio, vedere [Indicizzazione di set di dati di grandi dimensioni](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indicizzazione parallela

Una seconda opzione consiste nel configurare una strategia di indicizzazione parallela. Per requisiti di indicizzazione impegnativi da un punto di vista computazionale e non comuni, ad esempio OCR su documenti scansionati in una pipeline di ricerca cognitiva, una strategia di indicizzazione parallela potrebbe essere l'approccio giusto per questo obiettivo specifico. In una pipeline di arricchimento della ricerca cognitiva, l'elaborazione del linguaggio naturale e dell'analisi delle immagini richiedono un'esecuzione prolungata. L'indicizzazione parallela in un servizio che non gestisce contemporaneamente le richieste di query potrebbe essere un'opzione valida da usare su una parte di contenuto a elaborazione lenta di grandi dimensioni. 

Una strategia per l'elaborazione parallela presenta questi elementi:

+ Dividere i dati di origine in più contenitori o in più cartelle virtuali all'interno dello stesso contenitore. 
+ Eseguire il mapping di ogni mini set di dati in un'[origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) abbinata al relativo [indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Per la ricerca cognitiva, fare riferimento allo stesso [set di competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) in ogni definizione dell'indicizzatore.
+ Scrivere nello stesso indice di ricerca di destinazione. 
+ Pianificare l'esecuzione di tutti gli indicizzatori nello stesso momento.

> [!Note]
> Ricerca di Azure non consente di dedicare le repliche o le partizioni a carichi di lavoro specifici. Il rischio di indicizzazioni pesante simultanee sovraccarica il sistema a scapito delle prestazioni di query. In un ambiente di testing implementare prima l'indicizzazione parallela per comprendere i compromessi.

## <a name="configure-parallel-indexing"></a>Configurare l'indicizzazione parallela

Per gli indicizzatori la capacità di elaborazione si basa vagamente su un sottosistema dell'indicizzatore per ogni unità di servizio usata dal servizio di ricerca. Nei servizi di Ricerca di Azure con provisioning nei livelli Basic o Standard aventi almeno due repliche sono possibili più indicizzatori simultanei. 

1. Nella pagina **Panoramica** della dashboard del servizio di ricerca del [portale di Azure](https://portal.azure.com) controllare il **Piano tariffario** per confermarne la possibilità di gestione dell'indicizzazione parallela. I livelli Basic e Standard offrono più repliche.

2. In **Impostazioni** > **Scalabilità** [aumentare le repliche](search-capacity-planning.md) per l'elaborazione parallela: una replica aggiuntiva per ogni carico di lavoro dell'indicizzatore. Indicare un numero sufficiente per il volume di query esistente. Sacrificare i carichi di lavoro delle query per l'indicizzazione non è un buon compromesso.

3. Distribuire i dati in più contenitori a un livello raggiungibile dagli indicizzatori di Ricerca di Azure. Potrebbe trattarsi di tabelle multiple nel database SQL di Azure, contenitori multipli nel servizio Archiviazione BLOB di Azure o di raccolte multiple. Definire un oggetto di origine dati per ogni tabella o contenitore.

4. Creare e pianificare indicizzatori multipli da eseguire in parallelo:

   + Si supponga di avere un servizio con sei repliche. Configurare i sei indicizzatori, ognuno dei quali mappato in un'origine dati contenente un sesto del set di dati per una divisione in sei parti dell'intero set di dati. 

   + Puntare ogni indicizzatore allo stesso indice. Per i carichi di lavoro di ricerca cognitivi puntare ogni indicizzatore allo stesso set competenze.

   + In ogni definizione dell'indicizzatore pianificare lo stesso modello della fase di esecuzione. Ad esempio, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` crea una pianificazione il 15-05-2018 in tutti gli indicizzatori, con un'esecuzione a intervalli di otto ore.

All'ora pianificata tutti gli indicizzatori avviano l'esecuzione, caricando i dati, applicando gli arricchimenti, se è stata configurata una pipeline di ricerca cognitiva, e avviando operazioni di scrittura nell'indice. Ricerca di Azure non consente di bloccare l'indice per gli aggiornamenti. Vengono gestite le scritture simultanee, con un nuovo tentativo se un'operazione di scrittura specifica non riesce al primo tentativo.

> [!Note]
> Quando si aumentano le repliche, è opportuno aumentare il conteggio delle partizioni se le dimensioni dell'indice sono state pensate per un aumento significativo. Le partizioni archiviano sezioni di contenuto indicizzato: più sono le partizioni, più piccola è la sezione che ognuna deve archiviare.

## <a name="see-also"></a>Vedere anche 

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Ricerca di Azure](search-security-overview.md)