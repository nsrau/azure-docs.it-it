---
title: Anteprima dell'API REST per ricerca di Azure 2019-05-06-anteprima-ricerca di Azure
description: L'API REST di Azure servizio di ricerca versione 2019-05-06-Preview include funzionalità sperimentali, ad esempio l'archivio delle informazioni e le chiavi di crittografia gestite dal cliente.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649988"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>API REST di Azure servizio di ricerca-versione 2019-05-06-Preview
Questo articolo descrive la versione `api-version=2019-05-06-Preview` dell'API REST per il servizio Ricerca di Azure, che fornisce funzionalità sperimentali non ancora disponibili a livello generale.

> [!NOTE]
> Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti e sono soggette a modifiche. È consigliabile evitare l'uso delle API di anteprima in applicazioni di produzione.


## <a name="new-in-2019-05-06-preview"></a>Novità in 2019-05-06-Preview

[**Archivio informazioni**](knowledge-store-concept-intro.md) è una nuova destinazione di una pipeline di arricchimento basata su intelligenza artificiale. Oltre a un indice, è ora possibile salvare in modo permanente le strutture di dati popolate create durante l'indicizzazione in archiviazione di Azure. Si controllano le strutture fisiche dei dati tramite gli elementi in un elemento Skills, tra cui la modalità di data shaping, i dati archiviati nell'archiviazione tabelle o nell'archiviazione BLOB e se sono presenti più viste.

Anche le [**chiavi di crittografia gestite dal cliente**](search-security-manage-encryption-keys.md) per la crittografia sul lato servizio sono una nuova funzionalità di anteprima. Oltre alla crittografia inattiva integrata gestita da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui l'utente è l'unico proprietario delle chiavi.

## <a name="other-preview-features"></a>Altre funzionalità di anteprima

Le funzionalità annunciate nelle anteprime precedenti sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2019-05-06-Preview` senza modifiche al comportamento previsto.

+ Il [parametro di query moreLikeThis](search-more-like-this.md) trova i documenti pertinenti a un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. 
* L'indicizzazione di [BLOB CSV](search-howto-index-csv-blobs.md) crea un documento per riga, anziché un documento per ogni BLOB di testo.
* Il [supporto dell'API MongoDB per Cosmos DB indicizzatori](search-howto-index-cosmosdb.md) è in anteprima.


## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Ricerca di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione di riferimento dell'API REST di Azure servizio di ricerca. Se si verificano problemi, richiedere assistenza in [StackOverflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/)