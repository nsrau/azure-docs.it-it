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
ms.openlocfilehash: a4daaac6abababcedb5d6dd7eb2122ef29846ef4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792540"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>API REST del servizio ricerca cognitiva di Azure-versione 2019-05-06-Preview

Questo articolo descrive la versione `api-version=2019-05-06-Preview` dell'API REST del servizio di ricerca, che offre funzionalità sperimentali non ancora disponibili a livello generale.

> [!NOTE]
> Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti e sono soggette a modifiche. È consigliabile evitare l'uso delle API di anteprima in applicazioni di produzione.


## <a name="new-in-2019-05-06-preview"></a>Novità in 2019-05-06-Preview

[**Archivio informazioni**](knowledge-store-concept-intro.md) è una nuova destinazione di una pipeline di arricchimento basata su intelligenza artificiale. Oltre a un indice, è ora possibile salvare in modo permanente le strutture di dati popolate create durante l'indicizzazione in archiviazione di Azure. Si controllano le strutture fisiche dei dati tramite gli elementi in un elemento Skills, tra cui la modalità di data shaping, i dati archiviati nell'archiviazione tabelle o nell'archiviazione BLOB e se sono presenti più viste.

Anche le [**chiavi di crittografia gestite dal cliente**](search-security-manage-encryption-keys.md) per la crittografia sul lato servizio sono una nuova funzionalità di anteprima. Oltre alla crittografia inattiva integrata gestita da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui l'utente è l'unico proprietario delle chiavi.

## <a name="other-preview-features"></a>Altre funzionalità di anteprima

Le funzionalità annunciate nelle anteprime precedenti sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2019-05-06-Preview` senza modifiche al comportamento previsto.

+ Il [parametro di query moreLikeThis](search-more-like-this.md) trova i documenti pertinenti a un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. 
* L' [indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md) crea un documento per riga, anziché un documento per ogni BLOB di testo.
* Il [supporto dell'API MongoDB per Cosmos DB indicizzatori](search-howto-index-cosmosdb.md) è in anteprima.


## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Il servizio ricerca cognitiva di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione di riferimento dell'API REST di ricerca. Se si verificano problemi, richiedere assistenza in [StackOverflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/)