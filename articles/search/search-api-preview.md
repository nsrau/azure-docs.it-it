---
title: Funzionalità in anteprima nell'API REST
titleSuffix: Azure Cognitive Search
description: L'API REST di Azure ricerca cognitiva Service versione 2019-05-06-Preview include funzionalità sperimentali come l'archivio delle informazioni e la memorizzazione nella cache dell'indicizzatore per l'arricchimento incrementale.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162277"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funzionalità in anteprima in Azure ricerca cognitiva

Questo articolo elenca le funzionalità attualmente disponibili in anteprima. Le funzionalità che passano dalla versione di anteprima alla disponibilità generale vengono rimosse da questo elenco. È possibile controllare [gli aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=search) o le [novità per gli](whats-new.md) annunci relativi alla disponibilità generale.

Mentre alcune funzionalità di anteprima potrebbero essere disponibili nel portale e in .NET SDK, l'API REST include sempre funzionalità di anteprima.

+ Per le operazioni di ricerca, [ **`2019-05-06-Preview`** ](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) è la versione di anteprima corrente.
+ Per le operazioni di gestione, [ **`2019-10-01-Preview`** ](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) è la versione di anteprima corrente.

> [!IMPORTANT]
> La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funzionalità di arricchimento di intelligenza artificiale

Esplora i miglioramenti più recenti per l'arricchimento di intelligenza artificiale tramite l' [API di ricerca di anteprima](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Abilità ricerca entità personalizzata (anteprima)](cognitive-search-skill-custom-entity-lookup.md ) | Abilità cognitiva che cerca il testo da un elenco personalizzato di parole e frasi definito dall'utente. Utilizzando questo elenco, vengono etichettati tutti i documenti con le entità corrispondenti. L'abilità supporta inoltre un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non esatte. | 
| [Abilità rilevamento informazioni personali (anteprima)](cognitive-search-skill-pii-detection.md) | Abilità cognitiva usata durante l'indicizzazione che estrae informazioni personali da un testo di input e ti offre la possibilità di mascherarle da tale testo in diversi modi.| 
| [Arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) | Aggiunge la memorizzazione nella cache a una pipeline di arricchimento, consentendo di riutilizzare l'output esistente se una modifica di destinazione, ad esempio un aggiornamento a un oggetto di competenze o a un altro oggetto, non modifica il contenuto. La memorizzazione nella cache si applica solo ai documenti arricchiti prodotti da un skillt.| 
| [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md) | Nuova destinazione di una pipeline di arricchimento basata su intelligenza artificiale. La struttura dei dati fisici esiste nell'archiviazione BLOB di Azure e nell'archiviazione tabelle di Azure e viene creata e popolata quando si esegue un indicizzatore con un sistema di competenze cognitive collegato. La definizione di un archivio informazioni viene specificata in una definizione di competenze. All'interno della definizione dell'archivio delle informazioni, è possibile controllare le strutture fisiche dei dati tramite elementi di *proiezione* che determinano la modalità di modellazione dei dati, se i dati vengono archiviati nell'archiviazione tabelle o nell'archiviazione BLOB e se sono presenti più viste.| 

## <a name="indexing-and-query-features"></a>Funzionalità di indicizzazione ed esecuzione di query

Le funzionalità di anteprima dell'indicizzatore sono disponibili nell'API di ricerca di anteprima. 

|||
|-|-|
| [Indicizzatore Cosmos DB](search-howto-index-cosmosdb.md) | Supporto per l'API MongoDB (anteprima), l'API Gremlin (anteprima) e i tipi di API API Cassandra (anteprima). | 
|  [Indicizzatore Azure Data Lake Storage Gen2 (anteprima)](search-howto-index-azure-data-lake-storage.md) | Indicizzare il contenuto e i metadati da Data Lake Storage Gen2.| 
| [parametro di query moreLikeThis (anteprima)](search-more-like-this.md) | Trova i documenti rilevanti per un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. | 

## <a name="management-features"></a>Funzionalità di gestione

|||
|-|-|
| [Supporto per endpoint privati](service-create-private-endpoint.md) | È possibile creare una rete virtuale con un client protetto, ad esempio una macchina virtuale, e quindi creare un servizio di ricerca che usa un endpoint privato. |
| Restrizione di accesso IP | Usando [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) dell'API REST di gestione, è possibile creare un servizio con restrizioni per gli indirizzi IP a cui è consentito l'accesso. |

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