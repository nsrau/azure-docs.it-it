---
title: Ricompilare un indice di Ricerca di Azure o aggiornare il contenuto ricercabile - Ricerca di Azure
description: Aggiungere nuovi elementi, aggiornare gli elementi o i documenti esistenti o eliminare i documenti obsoleti in una ricompilazione completa o in un'indicizzazione incrementale parziale per aggiornare un indice di Ricerca di Azure.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9c9af69e45af6a70c5327393a1c10385ba2c2aed
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316897"
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


## <a name="see-also"></a>Vedere anche 

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Ricerca di Azure](search-security-overview.md)