---
title: Esempi di informazioni dettagliate su Bing - Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene esempi di come Ricerca visiva Bing possibile usare e visualizzare informazioni dettagliate sulle immagini in Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 3d4e9bf67aa1d3df815c674fdc1e2019f68b4a60
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086704"
---
# <a name="examples-of-bing-insights-usage"></a>Esempi di utilizzo delle informazioni dettagliate di Bing

> [!WARNING]
> API di ricerca Bing passano da servizi cognitivi a Ricerca Bing Services. A partire dal **30 ottobre 2020** , le nuove istanze di ricerca Bing devono essere sottoposte a provisioning in base al processo documentato [qui](https://aka.ms/cogsvcs/bingmove).
> API di ricerca Bing provisioning con servizi cognitivi sarà supportato per i prossimi tre anni o fino alla fine del Enterprise Agreement, a seconda di quale evento si verifichi per primo.
> Per istruzioni sulla migrazione, vedere [ricerca Bing Services](https://aka.ms/cogsvcs/bingmigration).

Questo articolo contiene esempi del modo in cui Bing potrebbe usare e visualizzare informazioni dettagliate sulle immagini su Bing.com.

## <a name="pagesincluding-insight-example"></a>Esempio di informazioni dettagliate PagesIncluding

Di seguito viene visualizzato un collegamento alla prima pagina Web, che consente all'utente di espandere e comprimere l'elenco di altre pagine Web che includono l'immagine:

![Pagine espanse che includono l'immagine](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Esempio di informazioni dettagliate ShoppingSources

Di seguito viene illustrato come Bing potrebbe visualizzare le origini acquisti per i prodotti visualizzati nell'immagine:

![Fonti per l'acquisto](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Esempio di informazioni dettagliate VisualSearch

Di seguito viene illustrato come Bing potrebbe visualizzare immagini simili visivamente (vedere le **Immagini correlate** nell'esempio):

![Immagini visivamente simili](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Esempio di informazioni dettagliate Recipes

L'esempio seguente mostra in che modo Bing potrebbe visualizzare ricette per l'alimento mostrato nell'immagine. L'esempio consente all'utente di tenere presente che sono disponibili alcune ricette:

![Ricette e pagine che includono l'immagine](./media/recipes-pages-including.PNG)

 E fornisce il collegamento alle ricette quando l'utente espande l'elenco:

![Pagine di ricette espanse che includono l'immagine](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Esempio di informazioni dettagliate RelatedSearches

L'esempio seguente mostra in che modo Bing potrebbe visualizzare le ricerche correlate di immagini eseguite da altri utenti. Se l'utente fa clic sull'immagine, viene visualizzata la pagina dei risultati della ricerca Bing.com/images per la query correlata.

![Ricerche correlate per le immagini](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Esempio di informazioni dettagliate Entity

L'esempio seguente mostra in che modo Bing potrebbe visualizzare informazioni sull'entità (persona, luogo o cosa) mostrata nell'immagine. Se l'utente fa clic sul collegamento dell'entità, l'utente viene portato alla pagina dei risultati della ricerca di Bing.com per l'entità:

![Entità visualizzata in un'immagine](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visualizzazione di altre informazioni dettagliate che l'utente potrebbe esplorare

L'esempio seguente mostra in che modo Bing potrebbe visualizzare altre informazioni sull'immagine che l'utente può esplorare.

![Esplorare altre informazioni dettagliate sull'immagine](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Rettangoli di selezione e aree sensibili

I tag non predefiniti includono il rettangolo di selezione che identifica l'area di interesse nell'immagine a cui è applicato il tag. Se il rettangolo di selezione non identifica l'intera immagine, usarlo per creare un'area sensibile sull'immagine. L'utente può fare clic sull'area sensibile per ottenere informazioni relative al contenuto trovato sotto l'area sensibile (o il rettangolo). Se, ad esempio, l'immagine è un'immagine di alta moda, i risultati possono contenere tag (e caselle di delimitazione) per gli accessori mostrati nell'immagine, ad esempio una borsetta, gioielli, sciarpe e così via. Nell'esempio seguente viene illustrato un rettangolo sensibile per gli occhialini mostrati nell'immagine:

![Rettangolo di selezione e area sensibile](./media/click-to-search.PNG)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la prima richiesta, vedere le guide introduttive:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)





