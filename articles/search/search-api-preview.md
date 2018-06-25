---
title: API REST per il servizio Ricerca di Azure versione 2017-11-11-Preview | Microsoft Docs
description: L'API REST per il servizio Ricerca di Azure versione 2017-11-11-Preview include funzionalità sperimentali, ad esempio la ricerca di sinonimi e le ricerche moreLikeThis.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: ce5771777762414a0229cf83425c2f3601cb979a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659629"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>API REST per il servizio Ricerca di Azure: versione 2017-11-11-Preview
Questo articolo descrive la versione `api-version=2017-11-11-Preview` dell'API REST per il servizio Ricerca di Azure, che fornisce funzionalità sperimentali non ancora disponibili a livello generale.

> [!NOTE]
> Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti e sono soggette a modifiche. È consigliabile evitare l'uso delle API di anteprima in applicazioni di produzione.


## <a name="new-in-2017-11-11-preview"></a>Novità della versione 2017-11-11-Preview

[Ricerca cognitiva](cognitive-search-concept-intro.md), una nuova funzionalità di arricchimento di Ricerca di Azure che consente di trovare informazioni latenti in origini non di testo e testo indifferenziato, trasformandolo in contenuto full-text disponibile per la ricerca in Ricerca di Azure.

Le risorse seguenti vengono introdotte o modificate nell'API REST di anteprima. Tutte le altre API REST sono uguali se si richiama la versione disponibile a livello generale o la versione di anteprima.

+ [Creare un insieme di competenze (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)

+ [Creare un indicizzatore (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Competenze predefinite](cognitive-search-predefined-skills.md)

Tutte le altre API REST sono le stesse indipendentemente dall'impostazione della versione dell'API. Ad esempio `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` e `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (senza `Preview`) sono funzionalmente equivalenti.

## <a name="other-preview-features"></a>Altre funzionalità di anteprima

Le funzionalità annunciate nelle anteprime precedenti sono ancora in anteprima pubblica. Se si chiama un'API con una versione di anteprima precedente, è possibile continuare a usare tale versione o passare a `2017-11-11-Preview` senza modifiche al comportamento previsto.

+ La funzionalità di [indicizzazione di file CSV in BLOB di Azure](search-howto-index-csv-blobs.md), introdotta in `api-version=2015-02-28-Preview`, rimane in versione di anteprima. Questa funzionalità fa parte dell'indicizzazione BLOB di Azure e viene richiamata tramite un'impostazione di parametro. Ogni riga di un file CSV viene indicizzata come documento separato.

+ La funzionalità di [indicizzazione di matrici JSON in BLOB di Azure](search-howto-index-json-blobs.md), introdotta in `api-version=2015-02-28-Preview`, rimane in versione di anteprima. Questa funzionalità fa parte dell'indicizzazione BLOB di Azure e viene richiamata tramite un'impostazione di parametro. Ogni elemento della matrice viene indicizzato come documento separato.

+ Il [parametro di query moreLikeThis](search-more-like-this.md) trova i documenti pertinenti a un documento specifico. Questa funzionalità è stata presente nelle anteprime precedenti. 


## <a name="how-to-call-a-preview-api"></a>Come chiamare un'API di anteprima

Le anteprime precedenti sono ancora operative ma nel tempo non vengono aggiornate. Se il codice chiama `api-version=2016-09-01-Preview` o `api-version=2015-02-28-Preview`, tali chiamate sono ancora valide. Tuttavia, solo la versione di anteprima più recente viene aggiornata con miglioramenti. 

La sintassi di esempio seguente viene illustra una chiamata per la versione dell'API di anteprima.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Ricerca di Azure è disponibile in più versioni. Per altre informazioni, vedere le [versioni API](search-api-versions.md).
