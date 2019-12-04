---
title: Indicizzazione incrementale (anteprima)
titleSuffix: Azure Cognitive Search
description: Configurare la pipeline di arricchimento intelligenza artificiale per guidare i dati alla coerenza finale per gestire gli aggiornamenti per competenze, skillsets, indicizzatori o origini dati. Questa funzionalità è attualmente disponibile in anteprima pubblica
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c44228d7e1456bce870765935beb011cb24626d5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790941"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Che cos'è l'indicizzazione incrementale in Azure ricerca cognitiva?

> [!IMPORTANT] 
> L'indicizzazione incrementale è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

L'indicizzazione incrementale è una nuova funzionalità di Azure ricerca cognitiva che aggiunge la memorizzazione nella cache e lo stato al contenuto arricchito in un cognitivo, garantendo il controllo dell'elaborazione e della rielaborazione dei singoli passaggi in una pipeline di arricchimento. Non solo consente di mantenere l'investimento monetario nell'elaborazione, ma anche un sistema più efficiente. Quando le strutture e il contenuto vengono memorizzati nella cache, un indicizzatore può determinare quali competenze sono state modificate ed eseguire solo quelle che sono state modificate, oltre a eventuali competenze dipendenti a valle. 

Con l'indicizzazione incrementale, la versione corrente della pipeline di arricchimento fa il minimo del lavoro necessario per garantire la coerenza di tutti i documenti nell'indice. Per gli scenari in cui si desidera il controllo completo, è possibile utilizzare controlli con granularità fine per eseguire l'override dei comportamenti previsti. Per ulteriori informazioni sulla configurazione, vedere [configurare l'indicizzazione incrementale](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Cache dell'indicizzatore

L'indicizzazione incrementale aggiunge una cache dell'indicizzatore alla pipeline di arricchimento. L'indicizzatore memorizza nella cache i risultati del cracking dei documenti e gli output di ogni competenza per ogni documento. Quando un set di competenze viene aggiornato, vengono rieseguite solo le competenze modificate, o a valle. I risultati aggiornati vengono scritti nella cache e il documento viene aggiornato nell'indice e nell'archivio conoscenze.

Fisicamente, la cache è un account di archiviazione. Tutti gli indici in un servizio di ricerca possono condividere lo stesso account di archiviazione per la cache dell'indicizzatore. A ogni indicizzatore viene assegnato un identificatore di cache univoco e non modificabile.

### <a name="cache-configuration"></a>Configurazione della cache

È necessario impostare la proprietà `cache` nell'indicizzatore per iniziare a trarre vantaggio dall'indicizzazione incrementale. Nell'esempio seguente viene illustrato un indicizzatore con la memorizzazione nella cache abilitata. Nelle sezioni seguenti vengono descritte alcune parti specifiche di questa configurazione.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": {}
}
```

Se si imposta questa proprietà per la prima volta in un indicizzatore esistente, sarà necessario reimpostarla, che comporterà l'elaborazione di tutti i documenti nell'origine dati. L'obiettivo dell'indicizzazione incrementale consiste nel rendere i documenti nell'indice coerenti con l'origine dati e la versione corrente del set di competenze. La reimpostazione dell'indice è il primo passo per ottenere questa coerenza, poiché elimina gli eventuali documenti arricchiti dalle versioni precedenti del set di competenze. L'indicizzatore deve essere reimpostato per poter iniziare con una baseline coerente.

### <a name="cache-lifecycle"></a>Ciclo di vita della cache

Il ciclo di vita della cache è gestito dall'indicizzatore. Se la proprietà `cache` nell'indicizzatore è impostata su null o la stringa di connessione è stata modificata, la cache esistente verrà eliminata. Il ciclo di vita della cache è anche legato al ciclo di vita dell'indicizzatore. Se viene eliminato un indicizzatore, viene eliminata anche la cache associata.

### <a name="indexer-cache-mode"></a>Modalità della cache dell'indicizzatore

La cache dell'indicizzatore può funzionare in modalità in cui i dati vengono solo scritti nella cache oppure vengono scritti nella cache e usati per riarricchire i documenti.  È possibile sospendere temporaneamente l'arricchimento incrementale impostando la proprietà `enableReprocessing` nella cache su `false` e riprenderlo in seguito per ottenere la coerenza finale impostandola su `true`. Questo controllo è particolarmente utile quando si vuole dare la priorità all'indicizzazione dei nuovi documenti rispetto a garantire la coerenza fra tutti i documenti.

## <a name="change-detection-override"></a>Override del rilevamento delle modifiche

L'indicizzazione incrementale offre un controllo granulare su tutti gli aspetti della pipeline di arricchimento. Questo controllo consente di gestire le situazioni in cui una modifica potrebbe avere conseguenze indesiderate. Se ad esempio si modifica un set di competenze e si aggiorna l'URL di una competenza personalizzata, l'indicizzatore invaliderà i risultati memorizzati nella cache per tale competenza. Se occorre semplicemente trasferire l'endpoint in una macchina virtuale diversa o ridistribuire la competenza con una nuova chiave di accesso, si vorrà certamente evitare che i documenti esistenti vengano rielaborati.

Per assicurarsi che l'indicizzatore possa solo arricchire gli aggiornamenti richiesti in modo esplicito, gli aggiornamenti al set di competenze possono facoltativamente impostare il `disableCacheReprocessingChangeDetection` parametro QueryString su `true`. Se impostato, questo parametro garantisce che venga eseguito il commit solo degli aggiornamenti al set di competenze, senza valutare gli effetti della modifica sulla raccolta di documenti esistente.

Nell'esempio seguente viene illustrato l'utilizzo di QueryString. Fa parte della richiesta, con coppie chiave-valore separate da &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Invalidamento della cache

Lo scenario opposto è quello in cui viene distribuita una nuova versione di una competenza personalizzata, all'interno della pipeline di arricchimento non vengono apportate modifiche, ma è necessario che una competenza specifica venga invalidata e tutti i documenti interessati vengano rielaborati in modo da riflettere i vantaggi di un modello aggiornato. Nei casi di questo tipo è possibile chiamare l'operazione di invalidamento delle competenze sul set di competenze. L'API di reimpostazione delle competenze accetta una richiesta POST con l'elenco degli output delle competenze nella cache che deve essere invalidata. Per altre informazioni sull'API Reimposta le competenze, vedere [reimpostare l'indicizzatore (API REST di ricerca)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Rilevamento delle modifiche bidirezionali

Oltre a spostarsi in avanti ed elaborare i nuovi documenti, gli indicizzatori ora sono in grado anche di spostarsi indietro e rielaborare i documenti precedentemente elaborati per ottenere la coerenza. Con questa nuova funzionalità, è importante comprendere come risultano le modifiche apportate ai componenti della pipeline di arricchimento nel lavoro dell'indicizzatore. L'indicizzatore Accoda il lavoro da eseguire quando identifica una modifica invalidante o incoerente rispetto al contenuto memorizzato nella cache.

### <a name="invalidating-changes"></a>Modifiche invalidanti

Le modifiche invalidanti sono rare ma hanno un effetto significativo sullo stato della pipeline di arricchimento. Una modifica invalidante è una modifica che fa sì che l'intera cache non sia più valida. Un esempio di modifica invalidante è quello in cui l'origine dati viene aggiornata. Per gli scenari in cui si è certi che la modifica non deve invalidare la cache, ad esempio la rotazione della chiave nell'account di archiviazione, è necessario impostare il `ignoreResetRequirement` parametro QueryString su `true` sull'operazione di aggiornamento della risorsa specifica per assicurarsi che l'operazione non venga rifiutata.

Ecco l'elenco completo delle modifiche che, se apportate, invaliderebbero la cache:

* Modifica del tipo di origine dati
* Modifica del contenitore dell'origine dati
* Credenziali dell'origine dati
* Criteri di rilevamento delle modifiche all'origine dati
* Criteri di rilevamento delle eliminazioni di origini dati
* Mapping dei campi dell'indicizzatore
* Parametri dell'indicizzatore
    * Modalità di analisi
    * Estensioni escluse
    * Estensioni indicizzate
    * Solo metadati di archiviazione dell'indice per i documenti di dimensioni eccessive
    * Intestazioni di testo delimitate
    * Delimitatore di testo delimitato
    * Radice documento
    * Azione per immagini (modifiche alla modalità di estrazione delle immagini)

### <a name="inconsistent-changes"></a>Modifiche incoerenti

Un esempio di modifica incoerente è un aggiornamento del set di competenze che modifica una competenza. La modifica può rendere incoerente una parte della cache. L'indicizzatore identificherà il lavoro per ristabilire la coerenza.  

Ecco l'elenco completo delle modifiche che causano l'incoerenza della cache:

* Competenza nel set di competenze di tipo diverso Tipo OData della competenza aggiornato
* Parametri specifici delle competenze aggiornati, ad esempio l'URL, i valori predefiniti o altri parametri
* Modifiche agli output delle competenze, la competenza restituisce output aggiuntivi o diversi
* Aggiornamenti delle competenze che producono una cronologia diversa, il concatenamento delle competenze è cambiato, ad esempio gli input delle competenze
* Invalidamento di qualsiasi competenza a monte, se una competenza che fornisce un input a questa competenza viene aggiornata
* Aggiornamenti alla posizione di proiezione dell'archivio conoscenze, risultanti nella ripetizione della proiezione dei documenti
* Modifiche alle proiezioni dell'archivio conoscenze, risultanti nella ripetizione della proiezione dei documenti
* Modifiche ai mapping dei campi di output in un indicizzatore risultanti nella ripetizione della proiezione dei documenti nell'indice

## <a name="rest-api-reference-for-incremental-indexing"></a>Informazioni di riferimento sull'API REST per l'indicizzazione incrementale

REST `api-version=2019-05-06-Preview` fornisce le API per l'indicizzazione incrementale, con aggiunte a indicizzatori, skillsets e origini dati. La documentazione di riferimento attualmente non include queste aggiunte. Nella sezione seguente vengono descritte le modifiche dell'API.

### <a name="indexers"></a>Indicizzatori

[Crea indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer) e [Aggiorna indicizzatore](https://docs.microsoft.com/rest/api/searchservice/update-indexer) esporrà ora le nuove proprietà relative alla cache:

* `StorageAccountConnectionString`: la stringa di connessione all'account di archiviazione che verrà utilizzata per memorizzare nella cache i risultati intermedi.

* `CacheId`: il `cacheId` è l'identificatore del contenitore all'interno dell'account di archiviazione `annotationCache` che verrà usato come cache per questo indicizzatore. Questa cache sarà univoca per questo indicizzatore e, se l'indicizzatore viene eliminato e ricreato con lo stesso nome, `cacheId` verrà rigenerato. `cacheId` non può essere impostato, viene sempre generato dal servizio.

* `EnableReprocessing`: impostare su `true` per impostazione predefinita, se impostato su `false`, i documenti continueranno a essere scritti nella cache, ma nessun documento esistente verrà rielaborato in base ai dati della cache.

Alcuni indicizzatori (tramite [origini dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) recuperano i dati tramite query. Per le query che recuperano dati, gli indicizzatori supporteranno anche un nuovo parametro della stringa di query: `ignoreResetRequirement` deve essere impostato su `true` quando l'azione di aggiornamento non deve invalidare la cache.

### <a name="skillsets"></a>Set di competenze

Skillsets non supporterà alcuna nuova operazione, ma supporterà un nuovo parametro stringa di query : `disableCacheReprocessingChangeDetection` deve essere impostato su `true` quando non si vuole aggiornare i documenti esistenti in base all'azione corrente.

### <a name="datasources"></a>Datasources

Datasources non supporterà alcuna nuova operazione, ma supporterà un nuovo parametro stringa di query: `ignoreResetRequirement` deve essere impostato su `true` quando l'azione di aggiornamento non deve invalidare la cache.

## <a name="best-practices"></a>Procedure consigliate

L'approccio consigliato per l'utilizzo dell'indicizzazione incrementale consiste nel configurarla impostando la proprietà cache in un nuovo indicizzatore o reimpostando un indicizzatore esistente e impostando la proprietà cache.

Usare il `ignoreResetRequirement` sporadicamente perché potrebbe causare incoerenza indesiderata nei dati che non verranno rilevati facilmente.

## <a name="takeaways"></a>Risultati

L'indicizzazione incrementale è una potente funzionalità che estende il rilevamento delle modifiche dall'origine dati a tutti gli aspetti della pipeline di arricchimento, tra cui l'origine dati, la versione corrente del proprio insieme di competenze e l'indicizzatore. Con l'evolversi delle competenze, dei set di competenze o degli arricchimenti, la pipeline di arricchimento garantisce che venga svolto il minor lavoro possibile, pur continuando a elaborare i documenti fino alla coerenza finale.

## <a name="next-steps"></a>Passaggi successivi

Per acquisire familiarità con l'indicizzazione incrementale, aggiungere una cache a un indicizzatore esistente o aggiungere la cache durante la definizione di un nuovo indicizzatore.

> [!div class="nextstepaction"]
> [Configurare l'indicizzazione incrementale](search-howto-incremental-index.md)
