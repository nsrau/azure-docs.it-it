---
title: Usare i confini geografici per filtrare i risultati generati dall'API di ricerca di attività locali Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Usare questo articolo per informazioni su come filtrare i risultati della ricerca generati dall'API di ricerca di attività locali Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: baf2f2ed3487bbc8b680a54d86b42d27d76a0ba9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957229"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Usare i confini geografici per filtrare i risultati generati dall'API di ricerca di attività locali Bing

L'API di ricerca di attività locali Bing consente di impostare i confini dell'area geografica specifica in cui si vuole eseguire la ricerca usando il parametro di query `localCircularView` o `localMapView`. Assicurarsi di usare un solo parametro nelle query. 

Se un termine di ricerca contiene una località geografica esplicita, questa verrà automaticamente usata dall'API di ricerca di attività locali Bing per impostare i confini per i risultati della ricerca. Se, ad esempio, il termine di ricerca è `sailing in San Diego`, `San Diego` verrà usato come località ed eventuali altre località specificate nei parametri di query o nelle intestazioni utente verranno ignorate. 

Se nel termine di ricerca non viene rilevata una località geografica e con i parametri di query non viene specificata alcuna località geografica, l'API di ricerca di attività locali Bing tenterà di determinare la località dall'intestazione `X-Search-ClientIP` o `X-Search-Location` della richiesta. Se non è specificata nessuna delle due intestazioni, l'API determinerà la località dall'IP client della richiesta o dalle coordinate GPS per dispositivi mobili.

## <a name="localcircularview"></a>localCircularView

Il parametro `localCircularView` crea un'area geografica circolare intorno a un set di coordinate di latitudine/longitudine, definita da un raggio. Se si usa questo parametro, le risposte inviate dall'API di ricerca di attività locali Bing includeranno solo località all'interno di quest'area circolare, a differenza del parametro `localMapView`, che può includere anche località di poco esterne all'area di ricerca.

Per specificare un'area di ricerca geografica circolare, selezionare un valore di latitudine e uno di longitudine da usare come centro del cerchio e un raggio (in metri). Questo parametro può essere aggiunto alla fine di una stringa di query, ad esempio: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Query completa:

````
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
````

## <a name="localmapview"></a>localMapView

Il parametro `localMapView` consente di specificare un'area geografica rettangolare per la ricerca usando due set di coordinate per definirne gli angoli sudorientale e nordoccidentale. Se si usa questo parametro, le risposte inviate dall'API di ricerca di attività locali Bing possono includere località sia interne sia appena esterne all'area specificata, diversamente dal parametro `localCircularView`, che include solo località interne all'area di ricerca.

Per specificare un'area di ricerca rettangolare, selezionare due set di coordinate di latitudine/longitudine da usare come angoli sudorientale e nordoccidentale del perimetro di confine. Definire prima le coordinate sudorientali, come nell'esempio seguente: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Query completa:

````
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
````

## <a name="next-steps"></a>Passaggi successivi
- [Local Business Search Java quickstart](quickstarts/local-search-java-quickstart.md) (Guida introduttiva alla ricerca di attività locali in Java)
- [Local Business Search C# Quickstart](quickstarts/local-quickstart.md) (Guida introduttiva alla ricerca di attività locali in C#)
- [Local Business Search Node Quickstart](quickstarts/local-search-node-quickstart.md) (Guida introduttiva alla ricerca di attività locali in Node)
- [Local Business Search Python quickstart](quickstarts/local-search-python-quickstart.md) (Guida introduttiva alla ricerca di attività locali in Python)
