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
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111646"
---
# <a name="examples-of-bing-insights-usage"></a>Esempi di utilizzo delle informazioni dettagliate di Bing

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

Per iniziare a usare la prima richiesta, vedere le guide introduttive [C#](quickstarts/csharp.md) : | [Java](quickstarts/java.md) | [node. js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





