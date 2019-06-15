---
title: Anteprima API REST per ricerca 2019-05-06-anteprima di Azure - ricerca di Azure
description: Ricerca di Azure Service REST API Version 2019-05-06-Preview include funzionalità sperimentali, ad esempio l'archivio della Knowledge Base e le chiavi di crittografia gestite dal cliente.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5374ff896613dd8f8563a2054be8a92103e63fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523911"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure Search Service REST api-version 2019-05-06-Preview
Questo articolo descrive la versione `api-version=2019-05-06-Preview` dell'API REST per il servizio Ricerca di Azure, che fornisce funzionalità sperimentali non ancora disponibili a livello generale.

> [!NOTE]
> Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti e sono soggette a modifiche. È consigliabile evitare l'uso delle API di anteprima in applicazioni di produzione.


## <a name="new-in-2019-05-06-preview"></a>Novità in 2019-05-06-Preview

[**Archivio Knowledge** ](knowledge-store-concept-intro.md) è una nuova destinazione di una pipeline di arricchimento basato su intelligenza artificiale. Oltre a un indice, è ora possibile mantenere le strutture di dati popolati create durante l'indicizzazione in archiviazione di Azure. È possibile controllare le strutture fisiche dei dati tramite gli elementi in un insieme di competenze, tra cui come la forma dei dati, se i dati vengono archiviati in archiviazione tabelle o nell'archivio Blob, e se sono presenti più viste.

[**Le chiavi di crittografia gestite dal cliente** ](search-security-manage-encryption-keys.md) per lato servizio di crittografia inattivi sono anche una nuova funzionalità in anteprima. Oltre l'incorporati crittografia inattivi gestito da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui si è l'unica proprietaria dei tasti.

## <a name="other-preview-features"></a>Altre funzionalità di anteprima

Le funzionalità annunciate nelle anteprime precedenti sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2019-05-06-Preview` senza modifiche al comportamento previsto.

+ Il [parametro di query moreLikeThis](search-more-like-this.md) trova i documenti pertinenti a un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. 
* [L'indicizzazione di blob CSV](search-howto-index-csv-blobs.md) crea un documento per ogni riga, a differenza di un documento per ogni blob di testo.
* [Supporto dell'API MongoDB per gli indicizzatori di Cosmos DB](search-howto-index-cosmosdb.md) è disponibile in anteprima.


## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Ricerca di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione di riferimento API REST del servizio ricerca di Azure. Se si verificano problemi, richiedere assistenza sul [StackOverflow](https://stackoverflow.com/) oppure [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Servizio di ricerca riferimenti all'API REST](https://docs.microsoft.com/rest/api/searchservice/)