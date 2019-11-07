---
title: API REST versione 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: L'API REST di Azure ricerca cognitiva Service versione 2019-05-06-Preview include funzionalità sperimentali come l'archivio delle informazioni e le chiavi di crittografia gestite dal cliente.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720020"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>API REST del servizio ricerca cognitiva di Azure-versione 2019-05-06-Preview

Questo articolo descrive la versione `api-version=2019-05-06-Preview` dell'API REST del servizio di ricerca, che offre funzionalità sperimentali non ancora disponibili a livello generale.

> [!NOTE]
> Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti e sono soggette a modifiche. È consigliabile evitare l'uso delle API di anteprima in applicazioni di produzione.


## <a name="new-in-2019-05-06-preview"></a>Novità in 2019-05-06-Preview

+ L' [indicizzazione incrementale](cognitive-search-incremental-indexing-conceptual.md) è una nuova modalità di indicizzazione che consente di aggiungere lo stato e la memorizzazione nella cache a un oggetto di competenze, consentendo di riutilizzare l'output esistente quando le definizioni dei dati di origine, degli indicizzatori e delle competenze sono invariate. Questa funzionalità si applica solo agli arricchimenti definiti un competenze cognitive.

+ [Cosmos DB indicizzatore](search-howto-index-cosmosdb.md) supporta l'API MongoDB, l'API Gremlin e la API Cassandra.

+ [Azure Data Lake storage Gen2 indicizzatore](search-howto-index-azure-data-lake-storage.md) può indicizzare il contenuto e i metadati da data Lake storage Gen2.

+ L' [estrazione dei documenti (anteprima)](cognitive-search-skill-document-extraction.md) è una competenza cognitiva usata durante l'indicizzazione che consente di estrarre il contenuto di un file dall'interno di un skillt. In precedenza, il cracking del documento si verificava solo prima dell'esecuzione di Skills. Con l'aggiunta di questa competenza, è anche possibile eseguire questa operazione all'interno dell'esecuzione di competenze.

+ [Traduzione testo (anteprima)](cognitive-search-skill-text-translation.md) è una competenza cognitiva utilizzata durante l'indicizzazione che valuta il testo e, per ogni record, restituisce il testo convertito nel linguaggio di destinazione specificato.

+ [Archivio informazioni](knowledge-store-concept-intro.md) è una nuova destinazione di una pipeline di arricchimento basata su intelligenza artificiale. La struttura dei dati fisici esiste nell'archiviazione BLOB di Azure e nell'archiviazione tabelle di Azure e viene creata e popolata quando si esegue un indicizzatore con un sistema di competenze cognitive collegato. La definizione di un archivio informazioni viene specificata in una definizione di competenze. All'interno della definizione dell'archivio delle informazioni, è possibile controllare le strutture fisiche dei dati tramite elementi di *proiezione* che determinano la modalità di modellazione dei dati, se i dati vengono archiviati nell'archiviazione tabelle o nell'archiviazione BLOB e se sono presenti più viste.

+ Anche le [chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) per la crittografia sul lato servizio sono una nuova funzionalità di anteprima. Oltre alla crittografia inattiva integrata gestita da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui l'utente è l'unico proprietario delle chiavi.

## <a name="earlier-preview-features"></a>Funzionalità di anteprima precedenti

Le funzionalità annunciate nelle anteprime precedenti sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2019-05-06-Preview` senza modifiche al comportamento previsto.

+ Il [parametro di query moreLikeThis](search-more-like-this.md) trova i documenti pertinenti a un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. 

+ L' [indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md) crea un documento per riga, anziché un documento per ogni BLOB di testo.

## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Il servizio ricerca cognitiva di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione di riferimento dell'API REST di ricerca. Se si verificano problemi, richiedere assistenza in [StackOverflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/)