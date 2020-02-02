---
title: Funzionalità in anteprima nell'API REST
titleSuffix: Azure Cognitive Search
description: L'API REST di Azure ricerca cognitiva Service versione 2019-05-06-Preview include funzionalità sperimentali come l'archivio delle informazioni e la memorizzazione nella cache dell'indicizzatore per l'arricchimento incrementale.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 9985e7ac70c5851699839a95d1e23af4dcca35e7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935108"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funzionalità in anteprima in Azure ricerca cognitiva

Questo articolo elenca le funzionalità attualmente disponibili in anteprima. Le funzionalità che passano dalla versione di anteprima alla disponibilità generale vengono rimosse da questo elenco. È possibile controllare [gli aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=search) o le [novità per gli](whats-new.md) annunci relativi alla disponibilità generale.

Mentre alcune funzionalità di anteprima potrebbero essere disponibili nel portale e in .NET SDK, l'API REST include sempre funzionalità di anteprima. La versione dell'API di anteprima corrente è `2019-05-06-Preview`.

> [!IMPORTANT]
> La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

+ La [ricerca di entità personalizzata (anteprima)](cognitive-search-skill-custom-entity-lookup.md ) Cerca il testo da un elenco personalizzato di parole e frasi definito dall'utente. Utilizzando questo elenco, vengono etichettati tutti i documenti con le entità corrispondenti. L'abilità supporta inoltre un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non esatte. 

+ Il rilevamento delle informazioni [personali (anteprima)](cognitive-search-skill-pii-detection.md) è una competenza cognitiva usata durante l'indicizzazione che estrae informazioni personali da un testo di input e offre la possibilità di mascherarle da tale testo in diversi modi.

+ L' [arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) aggiunge la memorizzazione nella cache a una pipeline di arricchimento, consentendo di riutilizzare l'output esistente se una modifica di destinazione, ad esempio un aggiornamento a un skillt o a un altro oggetto, non modifica il contenuto. La memorizzazione nella cache si applica solo ai documenti arricchiti prodotti da un skillt.

+ [Cosmos DB indicizzatore](search-howto-index-cosmosdb.md) supporta l'API MongoDB (anteprima), l'API Gremlin (anteprima) e la API Cassandra (anteprima).

+ [Azure Data Lake storage Gen2 indicizzatore (anteprima)](search-howto-index-azure-data-lake-storage.md) può indicizzare il contenuto e i metadati da data Lake storage Gen2.

+ [Archivio informazioni (anteprima)](knowledge-store-concept-intro.md) è una nuova destinazione di una pipeline di arricchimento basata su intelligenza artificiale. La struttura dei dati fisici esiste nell'archiviazione BLOB di Azure e nell'archiviazione tabelle di Azure e viene creata e popolata quando si esegue un indicizzatore con un sistema di competenze cognitive collegato. La definizione di un archivio informazioni viene specificata in una definizione di competenze. All'interno della definizione dell'archivio delle informazioni, è possibile controllare le strutture fisiche dei dati tramite elementi di *proiezione* che determinano la modalità di modellazione dei dati, se i dati vengono archiviati nell'archiviazione tabelle o nell'archiviazione BLOB e se sono presenti più viste.

+ il [parametro di query moreLikeThis (anteprima)](search-more-like-this.md) consente di trovare documenti rilevanti per un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. 

## <a name="earlier-preview-features"></a>Funzionalità di anteprima precedenti

Le funzionalità annunciate nelle anteprime precedenti, se non sono state passate alla disponibilità a livello generale, sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2019-05-06-Preview` senza modifiche al comportamento previsto.

## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Il servizio ricerca cognitiva di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione di riferimento dell'API REST di ricerca. Se si verificano problemi, richiedere assistenza in [StackOverflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/)