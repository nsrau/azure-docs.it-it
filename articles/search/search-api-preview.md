---
title: Elenco delle funzionalità di anteprima
titleSuffix: Azure Cognitive Search
description: Le funzionalità di anteprima vengono rilasciate in modo che i clienti possano fornire feedback sulla progettazione e l'utilità. Questo articolo è un elenco completo di tutte le funzionalità attualmente disponibili in anteprima.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 983322800198246bcfaecec92fe0b2b74e587756
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952098"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funzionalità in anteprima in Azure ricerca cognitiva

Questo articolo è un elenco completo di tutte le funzionalità disponibili in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le funzionalità di anteprima che passano alla disponibilità generale vengono rimosse da questo elenco. Se una funzionalità non è elencata di seguito, è possibile presupporre che sia disponibile a livello generale. Per gli annunci relativi alla disponibilità generale, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=search) o [novità](whats-new.md).

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
| [**Azure Machine Learning (AML) Skill**](cognitive-search-aml-skill.md) | Arricchimento con intelligenza artificiale| Un nuovo tipo di competenze per integrare un endpoint di inferenza da Azure Machine Learning. Introduzione a [questa esercitazione](cognitive-search-tutorial-aml-custom-skill.md). | Usare l' [API REST di ricerca 2020-06-30-Preview](/rest/api/searchservice/) o 2019-05-06-Preview. Disponibile anche nel portale, in progettazione di competenze, supponendo che ricerca cognitiva e i servizi di Azure ML siano distribuiti nella stessa sottoscrizione. |
| [**parametro featuresMode**](/rest/api/searchservice/search-documents#featuresmode) | Pertinenza (Punteggio) | Espansione del Punteggio di pertinenza per includere i dettagli: Punteggio di somiglianza per campo, frequenza del termine per campo e numero di token univoci corrispondenti. È possibile utilizzare questi punti dati nelle soluzioni di assegnazione dei [punteggi personalizzate](https://github.com/Azure-Samples/search-ranking-tutorial). | Aggiungere questo parametro di query usando i [documenti di ricerca (REST)](/rest/api/searchservice/search-documents) con API-Version = 2020-06 -30-preview o 2019-05-06-Preview. |
| [**Sessioni di debug**](cognitive-search-debug-session.md) | Portale, arricchimento AI (competenze) | Editor di competenze in-Session utilizzato per analizzare e risolvere i problemi relativi a un insieme di competenze. Le correzioni applicate durante una sessione di debug possono essere salvate in un skillt nel servizio. | Solo portale, usando i collegamenti a metà pagina nella pagina Panoramica per aprire una sessione di debug. |
| [**Eliminazione temporanea BLOB nativi**](search-howto-index-changed-deleted-blobs.md) | Indicizzatori, BLOB di Azure| L'indicizzatore di archiviazione BLOB di Azure in Azure ricerca cognitiva riconoscerà i BLOB in uno stato di eliminazione temporanea e rimuoverà il documento di ricerca corrispondente durante l'indicizzazione. | Aggiungere questa impostazione di configurazione usando [Crea indicizzatore (REST)](/rest/api/searchservice/create-indexer) con API-Version = 2020-06 -30-Preview o API-Version = 2019-05-06-Preview. |
| [**Abilità ricerca entità personalizzata**](cognitive-search-skill-custom-entity-lookup.md ) | Arricchimento AI (competenze) | Abilità cognitiva che cerca il testo da un elenco personalizzato di parole e frasi definito dall'utente. Con questo elenco vengono etichettati tutti i documenti con entità corrispondenti. La competenza supporta anche un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non proprio esatte. | Fare riferimento a questa esperienza di anteprima usando l'editor di competenze nel portale o creare un insieme di [competenze (REST)](/rest/api/searchservice/create-skillset) con API-Version = 2020-06 -30-Preview o API-Version = 2019-05-06-Preview. |
| [**Abilità rilevamento informazioni personali**](cognitive-search-skill-pii-detection.md) | Arricchimento AI (competenze) | Abilità cognitiva usata durante l'indicizzazione che estrae informazioni personali da un testo di input e ti offre la possibilità di mascherarle da tale testo in diversi modi. | Fare riferimento a questa esperienza di anteprima usando l'editor di competenze nel portale o creare un insieme di [competenze (REST)](/rest/api/searchservice/create-skillset) con API-Version = 2020-06 -30-Preview o API-Version = 2019-05-06-Preview. |
| [**Arricchimento incrementale**](cognitive-search-incremental-indexing-conceptual.md) | Configurazione dell'indicizzatore| Aggiunge la memorizzazione nella cache a una pipeline di arricchimento, consentendo di riutilizzare l'output esistente se una modifica di destinazione, ad esempio un aggiornamento a un oggetto di competenze o a un altro oggetto, non modifica il contenuto. La memorizzazione nella cache si applica solo ai documenti arricchiti prodotti da un skillt.| Aggiungere questa impostazione di configurazione usando [Crea indicizzatore (REST)](/rest/api/searchservice/create-indexer) con API-Version = 2020-06 -30-Preview o API-Version = 2019-05-06-Preview. |
| [**Indicizzatore Cosmos DB: API MongoDB, API Gremlin, API Cassandra**](search-howto-index-cosmosdb.md) | Origine dati dell'indicizzatore | Per Cosmos DB, l'API SQL è disponibile a livello generale, ma le API MongoDB, Gremlin e Cassandra sono in anteprima. | Solo per Gremlin e Cassandra, [iscriversi per primo](https://aka.ms/azure-cognitive-search/indexer-preview) , in modo che il supporto possa essere abilitato per la sottoscrizione nel back-end. Le origini dati MongoDB possono essere configurate nel portale. In caso contrario, la configurazione dell'origine dati per tutte e tre le API è supportata usando [Create data source (REST)](/rest/api/searchservice/create-data-source) con API-Version = 2020-06 -30-Preview o API-Version = 2019-05-06-Preview. |
|  [**Indicizzatore di Azure Data Lake Storage Gen2**](search-howto-index-azure-data-lake-storage.md) | Origine dati dell'indicizzatore | Indicizzare il contenuto e i metadati da Data Lake Storage Gen2.| È necessario [iscriversi](https://aka.ms/azure-cognitive-search/indexer-preview) per consentire l'abilitazione del supporto per la sottoscrizione nel back-end. Accedere a questa origine dati usando [Create data source (REST)](/rest/api/searchservice/create-data-source) con API-Version = 2020-06 -30-Preview o API-Version = 2019-05-06-Preview. |
| [**moreLikeThis**](search-more-like-this.md) | Query | Trova i documenti rilevanti per un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. | Aggiungere questo parametro di query nelle chiamate di [Cerca documenti (REST)](/rest/api/searchservice/search-documents) con API-Version = 2020-06 -30-preview, 2019-05-06-preview, 2016-09-01-preview o 2017-11-11-Preview. |

## <a name="how-to-call-a-preview-rest-api"></a>Come chiamare un'API REST di anteprima

Azure ricerca cognitiva sempre versioni preliminari delle funzionalità sperimentali tramite l'API REST, quindi tramite le versioni provvisorie di .NET SDK.

Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti sulla progettazione e l'implementazione di funzionalità. Per questo motivo le funzionalità di anteprima sono soggette a modifiche nel tempo, che talvolta possono interromperne la compatibilità con le versioni precedenti. Sono quindi diverse dalle funzionalità di una versione disponibile a livello generale (GA), che sono stabili e con molta probabilità rimangono invariate, fatta eccezione per piccoli miglioramenti e correzioni compatibili con le versioni precedenti. Inoltre, le funzionalità di anteprima non sempre si trasformano in una versione GA.

Mentre alcune funzionalità di anteprima potrebbero essere disponibili nel portale e in .NET SDK, l'API REST include sempre funzionalità di anteprima.

+ Per le operazioni di ricerca, [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) è la versione di anteprima corrente.

+ Per le operazioni di gestione, [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) è la versione di anteprima corrente.

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2019-05-06-Preview` o `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview` , queste chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti.

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Il servizio ricerca cognitiva di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione di riferimento per l'API di anteprima REST di ricerca. Se si verificano problemi, chiedere assistenza per [stack overflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca (anteprima)](/rest/api/searchservice/index-preview)