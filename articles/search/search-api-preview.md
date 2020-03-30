---
title: Funzionalità di anteprima nell'API RESTPreview features in REST API
titleSuffix: Azure Cognitive Search
description: Il servizio Ricerca cognitiva di Azure VERSIONE REST API 2019-05-06-Preview include funzionalità sperimentali come il Knowledge Store e la memorizzazione nella cache dell'indicizzatore per l'arricchimento incrementale.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162277"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funzionalità di anteprima in Ricerca cognitiva di AzurePreview features in Azure Cognitive Search

Questo articolo elenca le funzionalità attualmente in anteprima. Le funzionalità che passano dall'anteprima alla disponibilità generale vengono rimosse da questo elenco. È possibile controllare [gli aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=search) o [Novità](whats-new.md) per gli annunci relativi alla disponibilità generale.

Mentre alcune funzionalità di anteprima potrebbero essere disponibili nel portale e in .NET SDK, l'API REST include sempre funzionalità di anteprima.

+ Per le [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) operazioni di ricerca, è la versione di anteprima corrente.
+ Per le [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) operazioni di gestione, è la versione di anteprima corrente.

> [!IMPORTANT]
> La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Caratteristiche di arricchimento dell'iA

Esplora i miglioramenti più recenti per l'arricchimento dell'iadinamica tramite [l'API di ricerca dell'anteprima.](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)

|||
|-|-|
| [Competenza Ricerca entità personalizzata (anteprima)Custom Entity Lookup skill (preview)](cognitive-search-skill-custom-entity-lookup.md ) | Un'abilità cognitiva che cerca il testo di un elenco personalizzato e definito dall'utente di parole e frasi. Utilizzando questo elenco, vengono etichettati tutti i documenti con tutte le entità corrispondenti. L'abilità supporta anche un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze che sono simili ma non del tutto esatte. | 
| [Abilità di rilevamento PII (anteprima)PII Detection skill (preview)](cognitive-search-skill-pii-detection.md) | Un'abilità cognitiva utilizzata durante l'indicizzazione che estrae le informazioni personali da un testo di input e ti dà la possibilità di mascherarle da quel testo in vari modi.| 
| [Arricchimento incrementale (anteprima)Incremental enrichment (preview)](cognitive-search-incremental-indexing-conceptual.md) | Aggiunge la memorizzazione nella cache a una pipeline di arricchimento, consentendo di riutilizzare l'output esistente se una modifica di destinazione, ad esempio un aggiornamento a un set di competenze o a un altro oggetto, non modifica il contenuto. La memorizzazione nella cache si applica solo ai documenti arricchiti prodotti da un set di competenze.| 
| [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md) | Nuova destinazione di una pipeline di arricchimento basata sull'iA. La struttura dei dati fisici esiste nell'archiviazione BLOB di Azure e nell'archiviazione tabelle di Azure e viene creata e popolata quando si esegue un indicizzatore con un set di competenze cognitive associato. La definizione di un archivio informazioni stesso viene specificata all'interno di una definizione di set di competenze. All'interno della definizione del Knowledge Store è possibile controllare le strutture fisiche dei dati tramite elementi di *proiezione* che determinano la forma dei dati, se i dati vengono archiviati nell'archivio tabelle o nell'archiviazione BLOB e se sono presenti più visualizzazioni.| 

## <a name="indexing-and-query-features"></a>Funzionalità di indicizzazione e query

Le funzionalità di anteprima dell'indicizzatore sono disponibili nell'API di ricerca di anteprima. 

|||
|-|-|
| [Indicizzatore di Cosmos DB](search-howto-index-cosmosdb.md) | Supporto per i tipi di API MongoDB API (anteprima), API Gremlin (anteprima) e Cassandra API (anteprima). | 
|  [Indicizzatore Gen2 di Azure Data Lake Storage (anteprima)Azure Data Lake Storage Gen2 indexer (preview)](search-howto-index-azure-data-lake-storage.md) | Indicizza il contenuto e i metadati da Data Lake Storage Gen2.| 
| [parametro di query moreLikeThis (anteprima)](search-more-like-this.md) | Trova i documenti rilevanti per un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. | 

## <a name="management-features"></a>Funzionalità di gestione

|||
|-|-|
| [Supporto per Private Endpoint](service-create-private-endpoint.md) | È possibile creare una rete virtuale con un client sicuro (ad esempio una macchina virtuale) e quindi creare un servizio di ricerca che usa l'endpoint privato. |
| Restrizione di accesso IP | Usando [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) l'API REST di gestione, è possibile creare un servizio con restrizioni sugli indirizzi IP a cui è consentito l'accesso agli indirizzi IP. |

## <a name="earlier-preview-features"></a>Funzionalità di anteprima precedenti

Le funzionalità annunciate nelle anteprime precedenti, se non sono passate alla disponibilità generale, sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2019-05-06-Preview` senza modifiche al comportamento previsto.

## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Il servizio Ricerca cognitiva di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione di riferimento dell'API REST di ricerca. Se riscontri problemi, chiedici aiuto su [StackOverflow](https://stackoverflow.com/) o [contatta il supporto.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Riferimento all'API REST del servizio di ricercaSearch service REST API Reference](https://docs.microsoft.com/rest/api/searchservice/)