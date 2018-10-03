---
title: Esempi di informazioni dettagliate su Bing - Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Questo articolo offre esempi di informazioni dettagliate di immagini visualizzate su Bing.com.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: ce0c056ace550d3db2f4f51030177436fb194e0f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219839"
---
# <a name="examples-of-bing-insights-usage"></a>Esempi di utilizzo delle informazioni dettagliate di Bing

Questa sezione contiene esempi del modo in cui Bing potrebbe visualizzare informazioni dettagliate su Bing.com.

## <a name="pagesincluding-insight-example"></a>Esempio di informazioni dettagliate PagesIncluding

L'esempio seguente mostra in che modo Bing potrebbe visualizzare le pagine Web che includono l'immagine. Visualizza un collegamento alla prima pagina Web e consente all'utente di espandere e comprimere l'elenco delle altre pagine Web che includono l'immagine.

![Pagine espanse che includono l'immagine](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>Esempio di informazioni dettagliate ShoppingSources

L'esempio seguente mostra in che modo Bing potrebbe visualizzare le fonti per l'acquisto per i prodotti visualizzati nell'immagine.

![Fonti per l'acquisto](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>Esempio di informazioni dettagliate VisualSearch

L'esempio seguente mostra in che modo Bing potrebbe visualizzare immagini visivamente simili (vedere **Immagini correlate** nell'esempio).

![Immagini visivamente simili](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Esempio di informazioni dettagliate Recipes

L'esempio seguente mostra in che modo Bing potrebbe visualizzare ricette per l'alimento mostrato nell'immagine. Informa l'utente che sono disponibili ricette.

![Ricette e pagine che includono l'immagine](./media/recipes-pages-including.PNG)

 E fornisce il collegamento alle ricette quando l'utente espande l'elenco.

![Pagine di ricette espanse che includono l'immagine](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>Esempio di informazioni dettagliate RelatedSearches

L'esempio seguente mostra in che modo Bing potrebbe visualizzare le ricerche correlate di immagini eseguite da altri utenti. Se l'utente fa clic sull'immagine, viene visualizzata la pagina dei risultati della ricerca Bing.com/images per la query correlata.

![Ricerche correlate per le immagini](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Esempio di informazioni dettagliate Entity

L'esempio seguente mostra in che modo Bing potrebbe visualizzare informazioni sull'entità (persona, luogo o cosa) mostrata nell'immagine. Se l'utente fa clic sul collegamento dell'entità, viene visualizzata la pagina dei risultati della ricerca Bing.com per l'entità.

![Entità visualizzata in un'immagine](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visualizzazione di altre informazioni dettagliate che l'utente potrebbe esplorare

L'esempio seguente mostra in che modo Bing potrebbe visualizzare altre informazioni sull'immagine che l'utente può esplorare.

![Esplorare altre informazioni dettagliate sull'immagine](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Rettangoli di selezione e aree sensibili

I tag non predefiniti includono il rettangolo di selezione che identifica l'area di interesse nell'immagine a cui è applicato il tag. Se il rettangolo di selezione non identifica l'intera immagine, usarlo per creare un'area sensibile sull'immagine. L'utente può fare clic sull'area sensibile per ottenere informazioni relative al contenuto trovato sotto l'area sensibile (o il rettangolo). Ad esempio, nel caso di un'immagine d'alta moda, i risultati possono contenere tag (e rettangoli di selezione) per accessori visualizzati nell'immagine, ad esempio una borsa, un gioiello, una sciarpa e così via. L'esempio seguente mostra un rettangolo con un'area sensibile per gli occhiali da sole visualizzati nell'immagine.

![Rettangolo di selezione e area sensibile](./media/click-to-search.PNG)



## <a name="next-steps"></a>Passaggi successivi

Per conoscere il codice JSON alla base di questi esempi, vedere [Default insights](default-insights-tag.md) (Informazioni dettagliate predefinite) e [JSON response](overview.md#the-response) (Risposta JSON).

Per imparare rapidamente a gestire la prima richiesta, vedere le guide introduttive: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





