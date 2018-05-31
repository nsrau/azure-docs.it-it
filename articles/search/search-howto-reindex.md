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
ms.openlocfilehash: f38054eaf2829149a496f840366b6f2f9e03e12b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942104"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Come ricompilare un indice di Ricerca di Azure

La ricompilazione di un indice ne modifica la struttura, alterando l'espressione fisica dell'indice nel servizio Ricerca di Azure. Al contrario, l'aggiornamento di un indice è un aggiornamento del solo contenuto per prelevare le modifiche più recenti da un'origine dati esterna collaborativa. Questo articolo contiene indicazioni su come aggiornare gli indici sia in modo strutturale che in modo sostanziale.

Le autorizzazioni di lettura e scrittura a livello di servizio sono necessarie per gli aggiornamenti dell'indice. A livello di programmazione, è possibile chiamare le API .NET o REST per una ricompilazione completa o un'indicizzazione incrementale del contenuto, con parametri che specificano le opzioni di aggiornamento. 

In genere gli aggiornamenti di un indice vengono eseguiti su richiesta. Tuttavia, per gli indici popolati mediante [indicizzatori](search-indexer-overview.md) specifici dell'origine, è possibile usare un'utilità di pianificazione predefinita. L'utilità di pianificazione supporta l'aggiornamento dei documenti con una frequenza di 15 minuti, fino a qualsiasi intervallo e modello necessario. Una velocità di aggiornamento più rapida richiede il push manuale degli aggiornamenti dell'indice, probabilmente tramite una doppia scrittura sulle transazioni, aggiornando sia le origini dati esterne che l'indice di Ricerca di Azure contemporaneamente.

## <a name="full-rebuilds"></a>Ricompilazione completa

Per molti tipi di aggiornamenti è necessaria una ricompilazione completa. Una ricompilazione completa fa riferimento all'eliminazione di un indice, sia dati sia metadati, seguita dal ripopolamento dell'indice da origini dati esterne. A livello di programmazione [eliminare](https://docs.microsoft.com/rest/api/searchservice/delete-index), [creare](https://docs.microsoft.com/rest/api/searchservice/create-index) e [ricaricare](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) l'indice per ricompilarlo. 

Successive alla ricompilazione, tenere presente che se si sono eseguiti dei test sui modelli di query e i profili di punteggio, è possibile prevedere variazioni nei risultati della query se il contenuto sottostante è stato modificato.

## <a name="when-to-rebuild"></a>Quando ricompilare

Pianificare ricompilazioni complete e frequenti durante la fase di sviluppo attivo, quando gli schemi dell'indice sono in stato di flusso.

| Modifica | Stato della ricompilazione|
|--------------|---------------|
| Modificare il nome di un campo, il tipo di dati o i relativi [attributi degli indici](https://docs.microsoft.com/rest/api/searchservice/create-index) | La modifica della definizione di un campo comporta in genere una penalità di ricompilazione, fatta eccezione per gli [attributi dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index) seguenti: Retrievable, SearchAnalyzer, SynonymMaps. È possibile aggiungere gli attributi Retrievable, SearchAnalyzer e SynonymMaps a un campo esistente senza doverne ricompilare l'indice.|
| Aggiungere un campo | Nessun requisito obbligatorio nella ricompilazione. Ai documenti indicizzati esistenti vengono assegnati un valore null per il nuovo campo. In una reindicizzazione futura i valori dei dati di origine sostituiscono i valori null aggiunti da Ricerca di Azure. |
| Eliminare un campo | Non è possibile eliminare direttamente un campo da un indice di Ricerca di Azure. Al contrario, è necessario che l'applicazione ignori il campo "eliminato" per evitare di usarlo. Fisicamente la definizione del campo e i contenuti rimangono nell'indice fino alla successiva ricompilazione dell'indice mediante uno schema che omette il campo in questione.|

> [!Note]
> Anche se si passa da un livello è all'altro è necessario eseguire una ricompilazione. Se a un certo punto si decide di aggiungere capacità, non è possibile eseguire un aggiornamento sul posto. Nel nuovo punto di capacità è necessario creare un nuovo servizio e compilare gli indici da zero nel nuovo servizio. 

## <a name="partial-or-incremental-indexing"></a>Indicizzazione parziale o incrementale

Quando l'indice è in fase di produzione, l'attenzione passa all'indicizzazione incrementale, in genere senza interruzioni del servizio distinguibili. L'indicizzazione parziale o incrementale è un carico di lavoro del solo contenuto che consente di sincronizzare il contenuto di un indice di ricerca in modo da riflettere lo stato del contenuto in un'origine dati collaborativa. Un documento aggiunto o eliminato nell'origine viene aggiunto o eliminato all'indice. Nel codice chiamare l'operazione [Add, Update or Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare i documenti) o l'operazione equivalente di .NET.

> [!Note]
> Quando si usano gli indicizzatori che eseguono una ricerca per indicizzazione delle origini dati esterne, vengono usati meccanismi di rilevamento delle modifiche nei sistemi di origine per l'indicizzazione incrementale. Per l'[Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) viene usato un campo `lastModified`. Nell'[archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection) `timestamp` svolge lo stesso ruolo. Analogamente, sia l'[indicizzatore di database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) che l'[indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) contengono campi per contrassegnare gli aggiornamenti di riga. Per altre informazioni sugli indici, vedere la [panoramica sugli indicizzatori](search-indexer-overview.md).

## <a name="scale-out-indexing"></a>Scalabilità orizzontale dell'indicizzazione

Man mano che il volume dei dati aumenta o l'elaborazione richiede delle modifiche, è possibile che le ricompilazioni e i processi di reindicizzazione non risultino così semplici. Come primo passo per soddisfare le richieste, si consiglia di aumentare la [scalabilità e la capacità](search-capacity-planning.md) entro i limiti del servizio esistente. 

Se è possibile usare gli [indicizzatori](search-indexer-overview.md), saranno disponibili anche i meccanismi di scalabilità orizzontale aggiuntivi. Gli indicizzatori sono dotati di un'utilità di pianificazione predefinita che consente di suddividere l'indicizzazione in intervalli regolari o di estendere l'elaborazione oltre la finestra di 24 ore. Per di più, quando abbinati alle definizioni delle origini dati, gli indicizzatori consentono di ottenere un modulo di parallelismo mediante il partizionamento dei dati e l'uso delle pianificazioni da eseguire in parallelo.

### <a name="scheduled-indexing-for-large-data-sets"></a>Indicizzazione pianificata per grandi set di dati

La pianificazione è un meccanismo importante per l'elaborazione di grandi set di dati e le analisi a esecuzione lenta, ad esempio, analisi delle immagini in una pipeline di ricerca cognitiva. L'elaborazione degli indicizzatori funziona entro una finestra di 24 ore. Se l'elaborazione non viene completata nell'arco di 24 ore, i comportamenti della pianificazione dell'indicizzatore possono essere usati a proprio vantaggio. 

Per impostazione predefinita, l'indicizzazione pianificata viene avviata a intervalli specifici, in cui un processo viene in genere completato prima di riprendere nell'intervallo pianificato successivo. Tuttavia, se l'elaborazione non viene completata entro l'intervallo, l'indicizzatore si arresta perché il tempo a disposizione è terminato. All'intervallo successivo l'elaborazione riprende da dove si è interrotta perché il sistema tiene traccia del punto in cui si è verificata l'interruzione. 

In pratica per i caricamenti di indici che richiedono più giorni, è possibile inserire l'indicizzatore in una pianificazione di 24 ore. Quando l'indicizzazione riprende nelle successive 24 ore, riavvia l'ultimo documento valido e noto. In questo modo un indicizzatore può continuare a funzionare tramite un backlog del documento per più giorni fino a quando non vengono elaborati tutti i documenti non elaborati. Per altre informazioni su questo approccio, vedere [Indicizzazione di set di dati di grandi dimensioni](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indicizzazione parallela

Una seconda opzione consiste nel configurare una strategia di indicizzazione parallela. Per requisiti di indicizzazione impegnativi da un punto di vista computazionale e non comuni, ad esempio OCR su documenti scansionati in una pipeline di ricerca cognitiva, una strategia di indicizzazione parallela potrebbe essere l'approccio giusto per questo obiettivo specifico. In una pipeline di arricchimento della ricerca cognitiva, l'elaborazione del linguaggio naturale e dell'analisi delle immagini richiedono un'esecuzione prolungata. L'indicizzazione parallela in un servizio che non gestisce contemporaneamente le richieste di query potrebbe essere un'opzione valida da usare su una parte di contenuto a elaborazione lenta di grandi dimensioni. 

Una strategia per l'elaborazione parallela presenta questi elementi:

+ Dividere i dati di origine in più contenitori o in più cartelle virtuali all'interno dello stesso contenitore. 
+ Eseguire il mapping di ogni mini set di dati in un'[origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) abbinata al relativo [indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Per la ricerca cognitiva, fare riferimento allo stesso [set di competenze](ref-create-skillset.md) in ogni definizione dell'indicizzatore.
+ Scrivere nello stesso indice di ricerca di destinazione. 
+ Pianificare l'esecuzione di tutti gli indicizzatori nello stesso momento.

> [!Note]
> Ricerca di Azure non consente di dedicare le repliche o le partizioni a carichi di lavoro specifici. Il rischio di indicizzazioni pesante simultanee sovraccarica il sistema a scapito delle prestazioni di query. In un ambiente di testing implementare prima l'indicizzazione parallela per comprendere i compromessi.

### <a name="configure-parallel-indexing"></a>Configurare l'indicizzazione parallela

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