---
title: Introduzione all'indicizzazione incrementale (anteprima)
titleSuffix: Azure Cognitive Search
description: Configurare la pipeline di arricchimento intelligenza artificiale per guidare i dati alla coerenza finale per gestire gli aggiornamenti per competenze, skillsets, indicizzatori o origini dati.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ea3bcfc25040f09b6871d85412ac64061ec2f9e8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549123"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Che cos'è l'indicizzazione incrementale in Azure ricerca cognitiva?

> [!Note]
> L'indicizzazione incrementale è in anteprima e non è destinata all'uso in produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.
>

L'indicizzazione incrementale è una nuova funzionalità di Azure ricerca cognitiva che aggiunge la memorizzazione nella cache e lo stato al contenuto arricchito in un cognitivo, garantendo il controllo dell'elaborazione e della rielaborazione dei singoli passaggi in una pipeline di arricchimento. Non solo consente di mantenere l'investimento monetario nell'elaborazione, ma anche un sistema più efficiente. Quando le strutture e il contenuto vengono memorizzati nella cache, un indicizzatore può determinare quali competenze sono state modificate ed eseguire solo quelle che sono state modificate, oltre a eventuali competenze dipendenti a valle. 

Con l'indicizzazione incrementale, la versione corrente della pipeline di arricchimento è la quantità minima di lavoro per garantire la coerenza per tutti i documenti nell'indice. Per gli scenari in cui si desidera il controllo completo, è possibile utilizzare controlli con granularità fine per eseguire l'override dei comportamenti previsti. Per ulteriori informazioni sulla configurazione, vedere [configurare l'indicizzazione incrementale](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Cache indicizzatore

L'indicizzazione incrementale aggiunge una cache di indicizzatore alla pipeline di arricchimento. L'indicizzatore memorizza nella cache i risultati di cracking dei documenti e gli output di ogni competenza per ogni documento. Quando viene aggiornato un skillt, vengono rieseguite solo le competenze modificate o downstream. I risultati aggiornati vengono scritti nella cache e il documento viene aggiornato nell'indice e nell'archivio informazioni.

Fisicamente, la cache è un account di archiviazione. Tutti gli indici in un servizio di ricerca possono condividere lo stesso account di archiviazione per la cache dell'indicizzatore. A ogni indicizzatore viene assegnato un identificatore della cache univoco e non modificabile.

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
    "parameters":{}
}
```

Se si imposta questa proprietà per la prima volta in un indicizzatore esistente, sarà necessario reimpostarla, che comporterà l'elaborazione di tutti i documenti nell'origine dati. L'obiettivo dell'indicizzazione incrementale consiste nel rendere i documenti nell'indice coerenti con l'origine dati e la versione corrente del proprio insieme di competenze. La reimpostazione dell'indice è il primo passo verso questa coerenza, poiché elimina tutti i documenti arricchiti dalle versioni precedenti del set di competenze. L'indicizzatore deve essere reimpostato per iniziare con una baseline coerente.

### <a name="cache-lifecycle"></a>Ciclo di vita della cache

Il ciclo di vita della cache è gestito dall'indicizzatore. Se la proprietà `cache` nell'indicizzatore è impostata su null o la stringa di connessione è stata modificata, la cache esistente verrà eliminata. Il ciclo di vita della cache è associato anche al ciclo di vita dell'indicizzatore. Se viene eliminato un indicizzatore, viene eliminata anche la cache associata.

### <a name="indexer-cache-mode"></a>Modalità cache indicizzatore

La cache dell'indicizzatore può funzionare in modalità in cui i dati vengono scritti solo nella cache o i dati vengono scritti nella cache e usati per riarricchire i documenti.  È possibile sospendere temporaneamente l'arricchimento incrementale impostando la proprietà `enableReprocessing` nella cache su `false`e riprendendo in seguito l'arricchimento incrementale e determinando la coerenza finale impostando il `true`. Questo controllo è particolarmente utile quando si vuole classificare in ordine di priorità l'indicizzazione di nuovi documenti in modo da garantire la coerenza tra i documenti.

## <a name="change-detection-override"></a>Override del rilevamento delle modifiche

L'indicizzazione incrementale offre un controllo granulare su tutti gli aspetti della pipeline di arricchimento. Questo controllo consente di gestire le situazioni in cui una modifica potrebbe avere conseguenze impreviste. Se ad esempio si modifica un gruppo di competenze e si aggiorna l'URL per un'abilità personalizzata, l'indicizzatore invalida i risultati memorizzati nella cache per tale capacità. Se si trasferisce solo l'endpoint in una macchina virtuale diversa o si ridistribuisce la propria competenza con una nuova chiave di accesso, non si vuole che i documenti esistenti vengano rielaborati.

Per assicurarsi che l'indicizzatore possa solo arricchire gli aggiornamenti richiesti in modo esplicito, gli aggiornamenti al set di competenze possono facoltativamente impostare il `disableCacheReprocessingChangeDetection` parametro QueryString su `true`. Se impostato, questo parametro garantisce che vengano eseguiti solo gli aggiornamenti al set di competenze e che la modifica non venga valutata per gli effetti sul Corpus esistente.

Nell'esempio seguente viene illustrato l'utilizzo di QueryString. Fa parte della richiesta, con coppie chiave-valore separate da &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Invalidamento della cache

Il contrario di tale scenario è quello in cui è possibile distribuire una nuova versione di un'abilità personalizzata, nessuna delle modifiche all'interno della pipeline di arricchimento, ma è necessaria un'abilità specifica invalidata e tutti i documenti interessati vengono rielaborati in modo da riflettere i vantaggi di un modello aggiornato. In tali casi, è possibile chiamare l'operazione Invalidate Skills sul valore Skills. L'API Reimposta competenze accetta una richiesta POST con l'elenco degli output delle competenze nella cache che deve essere invalidata. Per altre informazioni sull'API Reimposta le competenze, vedere [reimpostare l'indicizzatore (API REST di ricerca)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Rilevamento delle modifiche bidirezionali

Gli indicizzatori non solo spostano in avanti ed elaborano nuovi documenti, ma ora sono in grado di spostarsi all'indietro e guidare i documenti precedentemente elaborati in modo coerente. Grazie a questa nuova funzionalità, è importante comprendere come le modifiche apportate ai componenti della pipeline di arricchimento comportino il lavoro dell'indicizzatore. L'indicizzatore Accoda il lavoro da eseguire quando identifica una modifica invalidante o incoerente rispetto al contenuto memorizzato nella cache.

### <a name="invalidating-changes"></a>Invalidare le modifiche

Le modifiche di invalidamento sono rare ma hanno un effetto significativo sullo stato della pipeline di arricchimento. Una modifica di invalidamento è quella in cui l'intera cache non è più valida. Un esempio di una modifica di invalidamento è quello in cui l'origine dati viene aggiornata. Per gli scenari in cui si è certi che la modifica non deve invalidare la cache, ad esempio la rotazione della chiave nell'account di archiviazione, è necessario impostare il `ignoreResetRequirement` parametro QueryString su `true` sull'operazione di aggiornamento della risorsa specifica per assicurarsi che l'operazione sia non rifiutato.

Di seguito è riportato l'elenco completo delle modifiche che potrebbero invalidare la cache:

* Modificare il tipo di origine dati
* Passa al contenitore dell'origine dati
* Credenziali origine dati
* Criteri di rilevamento delle modifiche all'origine dati
* Criteri di rilevamento eliminazione origini dati
* Mapping dei campi dell'indicizzatore
* Parametri dell'indicizzatore
    * Modalità di analisi
    * Estensioni del nome di file escluse
    * Estensioni di file indicizzate
    * Indicizzare i metadati di archiviazione solo per documenti sovradimensionati
    * Intestazioni di testo delimitate
    * Delimitatore di testo delimitato
    * Radice documento
    * Azione immagine (modifiche alla modalità di estrazione delle immagini)

### <a name="inconsistent-changes"></a>Modifiche incoerenti

Un esempio di modifica incoerente è un aggiornamento per il proprio Skills che modifica una competenza. La modifica può rendere incoerente una parte della cache. L'indicizzatore identificherà il lavoro per ristabilire la coerenza.  

Elenco completo delle modifiche risultante dall'incoerenza della cache:

* Il tipo di competenze è diverso. Il tipo di esperienza OData è stato aggiornato
* Parametri specifici dell'abilità aggiornati, ad esempio l'URL, i valori predefiniti o altri parametri
* Le competenze restituiscono le modifiche, la skill restituisce output aggiuntivi o diversi
* Gli aggiornamenti delle competenze risultanti sono origini diverse, il concatenamento delle competenze è cambiato, ovvero input skill
* Qualsiasi invalidamento di competenze upstream, se viene aggiornata un'abilità che fornisce un input a questa competenza
* Aggiornamenti al percorso di proiezione dell'archivio informazioni, risultati della riproiezione dei documenti
* Modifiche alle proiezioni dell'archivio delle informazioni, risultati della riproiezione dei documenti
* I mapping dei campi di output modificati in un indicizzatore comportano la riproiezione dei documenti nell'indice

## <a name="rest-api-reference-for-incremental-indexing"></a>Informazioni di riferimento sull'API REST per l'indicizzazione incrementale

REST `api-version=2019-05-06-Preview` fornisce le API per l'indicizzazione incrementale, con aggiunte a indicizzatori, skillsets e origini dati. La documentazione di riferimento attualmente non include queste aggiunte. Nella sezione seguente vengono descritte le modifiche dell'API.

### <a name="indexers"></a>Indicizzatori

[Crea indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer) e [Aggiorna indicizzatore](https://docs.microsoft.com/rest/api/searchservice/update-indexer) esporrà ora le nuove proprietà relative alla cache:

* `StorageAccountConnectionString`: la stringa di connessione all'account di archiviazione che verrà utilizzata per memorizzare nella cache i risultati intermedi.

* `CacheId`: il `cacheId` è l'identificatore del contenitore all'interno dell'account di archiviazione `annotationCache` che verrà usato come cache per questo indicizzatore. Questa cache sarà univoca per questo indicizzatore e se l'indicizzatore viene eliminato e ricreato con lo stesso nome, il `cacheId` verrà rigenerato. Non è possibile impostare la `cacheId`, che viene sempre generata dal servizio.

* `EnableReprocessing`: impostare su `true` per impostazione predefinita, se impostato su `false`, i documenti continueranno a essere scritti nella cache, ma nessun documento esistente verrà rielaborato in base ai dati della cache.

Alcuni indicizzatori (tramite [origini dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) recuperano i dati tramite query. Per le query che recuperano dati, gli indicizzatori supporteranno anche un nuovo parametro della stringa di query: `ignoreResetRequirement` deve essere impostato su `true` quando l'azione di aggiornamento non deve invalidare la cache.

### <a name="skillsets"></a>Set di competenze

Skillsets non supporterà alcuna nuova operazione, ma supporterà un nuovo parametro QueryString: `disableCacheReprocessingChangeDetection` deve essere impostato su `true` quando non si vuole aggiornare i documenti esistenti in base all'azione corrente.

### <a name="datasources"></a>DataSources

Le origini dati non supporteranno nuove operazioni, ma supporteranno un nuovo parametro QueryString: `ignoreResetRequirement` deve essere impostato su `true` quando l'azione di aggiornamento non deve invalidare la cache.

## <a name="best-practices"></a>Procedure consigliate

L'approccio consigliato per l'utilizzo dell'indicizzazione incrementale consiste nel configurare l'indicizzazione incrementale impostando la proprietà cache in un nuovo indicizzatore o reimpostando un indicizzatore esistente e impostando la proprietà cache.

Usare il `ignoreResetRequirement` sporadicamente perché potrebbe causare incoerenza indesiderata nei dati che non verranno rilevati facilmente.

## <a name="takeaways"></a>Risultati

L'indicizzazione incrementale è una potente funzionalità che estende il rilevamento delle modifiche dall'origine dati a tutti gli aspetti della pipeline di arricchimento, tra cui l'origine dati, la versione corrente del proprio insieme di competenze e l'indicizzatore. Con l'evolversi delle competenze, della skillsets o dell'arricchimento, la pipeline di arricchimento garantisce il minor lavoro possibile, pur continuando a guidare i documenti alla coerenza finale.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'indicizzazione incrementale, aggiungere una cache a un indicizzatore esistente o aggiungere la cache quando si definisce un nuovo indicizzatore.

> [!div class="nextstepaction"]
> [Configurare l'indicizzazione incrementale](search-howto-incremental-index.md)
