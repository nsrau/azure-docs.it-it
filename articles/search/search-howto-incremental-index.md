---
title: Aggiungi indicizzazione incrementale (anteprima)
titleSuffix: Azure Cognitive Search
description: Abilitare il rilevamento delle modifiche e mantenere lo stato del contenuto arricchito per l'elaborazione controllata in un Skills cognitivo. Questa funzionalità è attualmente in anteprima pubblica.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 09defe9648208e2300594169add990d4bcbd7a39
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112568"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Come configurare l'indicizzazione incrementale dei documenti arricchiti in Azure ricerca cognitiva

> [!IMPORTANT] 
> L'indicizzazione incrementale è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

Questo articolo illustra come aggiungere lo stato e la memorizzazione nella cache ai documenti arricchiti che passano attraverso una pipeline di arricchimento ricerca cognitiva di Azure in modo da poter indicizzare in modo incrementale i documenti da qualsiasi origine dati supportata. Per impostazione predefinita, un insieme di competenze è senza stato e la modifica di qualsiasi parte della relativa composizione richiede una ripetizione completa dell'indicizzatore. Con l'indicizzazione incrementale, l'indicizzatore può determinare quali parti della pipeline sono state modificate, riutilizzando gli arricchimenti esistenti per le parti non modificate e modificando i miglioramenti per i passaggi che cambiano. Il contenuto memorizzato nella cache viene inserito in archiviazione di Azure.

Se non si ha familiarità con la configurazione degli indicizzatori, iniziare con l' [indicizzatore Panoramica](search-indexer-overview.md) e quindi continuare con [skillsets](cognitive-search-working-with-skillsets.md) per informazioni sulle pipeline di arricchimento. Per ulteriori informazioni sui concetti chiave, vedere [indicizzazione incrementale](cognitive-search-incremental-indexing-conceptual.md).

## <a name="modify-an-existing-indexer"></a>Modificare un indicizzatore esistente

Se si dispone di un indicizzatore esistente, attenersi alla procedura seguente per abilitare l'indicizzazione incrementale.

### <a name="step-1-get-the-indexer"></a>Passaggio 1: ottenere l'indicizzatore

Iniziare con un indicizzatore esistente valido con l'origine dati e l'indice già definiti. L'indicizzatore deve essere eseguibile. Usando un client API, creare una [richiesta Get](https://docs.microsoft.com/rest/api/searchservice/get-indexer) per ottenere la configurazione corrente dell'indicizzatore a cui si desidera aggiungere l'indicizzazione incrementale.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Passaggio 2: aggiungere la proprietà cache

Modificare la risposta dalla richiesta GET per aggiungere la proprietà `cache` all'indicizzatore. L'oggetto cache richiede una sola proprietà, ovvero la stringa di connessione a un account di archiviazione di Azure.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>Passaggio 3: reimpostare l'indicizzatore

> [!NOTE]
> La reimpostazione dell'indicizzatore comporterà l'elaborazione di tutti i documenti nell'origine dati in modo che il contenuto possa essere memorizzato nella cache. Tutte le funzionalità di arricchimento cognitive verranno eseguite di nuovo in tutti i documenti.
>

Quando si configura l'indicizzazione incrementale per gli indicizzatori esistenti, è necessario reimpostare l'indicizzatore per assicurarsi che tutti i documenti siano in uno stato coerente. Reimpostare l'indicizzatore usando l' [API REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Passaggio 4: salvare la definizione aggiornata

Aggiornare la definizione dell'indicizzatore con una richiesta PUT, il corpo della richiesta deve contenere la definizione dell'indicizzatore aggiornata.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Se si emette un'altra richiesta GET nell'indicizzatore, la risposta dal servizio includerà una `cacheId` proprietà nell'oggetto cache. Il `cacheId` è il nome del contenitore che conterrà tutti i risultati memorizzati nella cache e lo stato intermedio di ogni documento elaborato da questo indicizzatore.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Abilitare l'indicizzazione incrementale nei nuovi indicizzatori

Per impostare l'indicizzazione incrementale per un nuovo indicizzatore, includere la proprietà `cache` nel payload della definizione dell'indicizzatore. Assicurarsi di usare la versione `2019-05-06-Preview` dell'API.

## <a name="overriding-incremental-indexing"></a>Override dell'indicizzazione incrementale

Quando è configurata, l'indicizzazione incrementale tiene traccia delle modifiche apportate alla pipeline di indicizzazione e determina la coerenza finale tra l'indice e le proiezioni. In alcuni casi, è necessario eseguire l'override di questo comportamento per assicurarsi che l'indicizzatore non esegua operazioni aggiuntive in seguito a un aggiornamento della pipeline di indicizzazione. Se ad esempio si aggiorna la stringa di connessione dell'origine dati, è necessario reimpostare l'indicizzatore e reindicizzare tutti i documenti quando l'origine dati è cambiata. Tuttavia, se si aggiorna solo la stringa di connessione con una nuova chiave, non si vuole che la modifica provochi aggiornamenti ai documenti esistenti. Viceversa, è possibile che l'indicizzatore invalide la cache e arricchisca i documenti anche se non viene apportata alcuna modifica alla pipeline di indicizzazione. È ad esempio possibile che si desideri invalidare l'indicizzatore se si ridistribuisce un'abilità personalizzata con un nuovo modello e si desidera rieseguire le competenze in tutti i documenti.

### <a name="override-reset-requirement"></a>Sostituisci requisito di reimpostazione

Quando si apportano modifiche alla pipeline di indicizzazione, le modifiche che comportano l'invalidamento della cache richiedono la reimpostazione dell'indicizzatore. Se si sta apportando una modifica alla pipeline dell'indicizzatore e non si vuole che il rilevamento delle modifiche invalide la cache, è necessario impostare il `ignoreResetRequirement` parametro QueryString su `true` per le operazioni nell'indicizzatore o nell'origine dati.

### <a name="override-change-detection"></a>Sostituisci rilevamento modifiche

Quando si eseguono aggiornamenti per il set di competenze che comportano il contrassegno dei documenti come incoerenti, ad esempio l'aggiornamento di un URL di competenze personalizzato quando viene ridistribuita l'abilità, impostare il parametro della stringa di query `disableCacheReprocessingChangeDetection` su `true` sugli aggiornamenti del set di competenze.

### <a name="force-change-detection"></a>Forza rilevamento modifiche

Quando si vuole che la pipeline di indicizzazione riconosca una modifica a un'entità esterna, ad esempio la distribuzione di una nuova versione di un'abilità personalizzata, è necessario aggiornare le competenze e "toccare" la competenza specifica modificando la definizione di competenze, in particolare l'URL da forzare rilevamento delle modifiche e invalidare la cache per tale capacità.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra l'indicizzazione incrementale per gli indicizzatori che includono skillsets. Per approfondire le proprie conoscenze, vedere gli articoli sulla reindicizzazione in generale, applicabili a tutti gli scenari di indicizzazione in Azure ricerca cognitiva.

+ [Come ricompilare un indice del ricerca cognitiva di Azure](search-howto-reindex.md). 
+ [Come indicizzare set di dati di grandi dimensioni in ricerca cognitiva di Azure](search-howto-large-index.md). 
