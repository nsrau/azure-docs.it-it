---
title: Come usare indicatori di effetto per evidenziare il testo - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare indicatori di effetto del testo ed evidenziazioni nei risultati della ricerca usando l'API Ricerca Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854032"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Uso degli indicatori di effetto per evidenziare il testo

Bing supporta l'evidenziazione dei risultati, che contrassegna i termini di query (o altri termini che Bing trova pertinenti) nelle stringhe di visualizzazione di alcune risposte. Ad esempio, i campi `name`, `displayUrl`, `snippet` e i risultati di una pagina Web potrebbero contenere termini di query contrassegnati. 

Per impostazione predefinita, Bing non include i marcatori di evidenziazione nelle stringhe visualizzate. Per abilitare i marcatori, includere il `textDecorations` parametro `true`di query nella richiesta e impostarlo su .

## <a name="hit-highlighting-example"></a>Esempio di evidenziazione dei risultati

Nell'esempio seguente viene `Sailing Dinghy`illustrato un risultato Web per . Bing ha contrassegnato l'inizio e la fine del termine di query utilizzando i caratteri Unicode E000 ed E001.
  
![Evidenziazione dei risultati](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Prima di visualizzare il risultato nell'interfaccia utente, sostituire i caratteri Unicode con quelli appropriati per il formato di visualizzazione.

## <a name="marker-formatting"></a>Formattazione dei marcatori

Bing offre la possibilità di utilizzare caratteri Unicode o tag HTML come marcatori. Per specificare i marcatori da utilizzare, includere il parametro di query [textFormat:](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 

| valore             | Marcatore                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caratteri Unicode (impostazione predefinita) |
| `textFormat=HTML` | Caratteri HTML              |

## <a name="additional-text-decorations"></a>Decorazioni di testo aggiuntive

Bing può restituire diverse decorazioni di testo diverse. Ad esempio, `Computation` una risposta può contenere indicatori `log(2)` di `expression` pedice per il termine di query nel campo.

![Marcatori in computation](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se la richiesta non specificasse le decorazioni, il `expression` campo conterrà `log10(2)`. 

Se `textDecorations` `true`è , Bing può includere i seguenti marcatori nelle stringhe di visualizzazione delle risposte. Se non è presente alcun tag HTML equivalente, la cella della tabella è vuota.

|Unicode|HTML|Descrizione
|-|-|-
|U+E000|\<b>|Contrassegna l'inizio del termine di query (evidenziazione dei risultati)
|U+E001|\</b>|Contrassegna la fine del termine di query
|U+E002|\<i&gt;|Contrassegna l'inizio di contenuto in corsivo 
|U+E003|\</i&gt;|Contrassegna la fine di contenuto in corsivo
|U+E004|\<br/>|Contrassegna un'interruzione di riga
|U+E005||Contrassegna l'inizio di un numero di telefono
|U+E006||Contrassegna la fine di un numero di telefono
|U+E007||Contrassegna l'inizio di un indirizzo
|U+E008||Contrassegna la fine di un indirizzo
|U+E009|\&nbsp;|Contrassegna uno spazio unificatore
|U+E00C|\<strong&gt;|Contrassegna l'inizio di contenuto in grassetto
|U+E00D|\</strong>|Contrassegna la fine di contenuto in grassetto
|U+E00E||Contrassegna l'inizio di contenuto con sfondo più chiaro rispetto allo sfondo circostante
|U+E00F||Contrassegna la fine di contenuto con sfondo più chiaro rispetto allo sfondo circostante
|U+E010||Contrassegna l'inizio di contenuto con sfondo più scuro rispetto allo sfondo circostante
|U+E011||Contrassegna la fine di contenuto con sfondo più scuro rispetto allo sfondo circostante
|U+E012|\<del>|Contrassegna l'inizio di contenuto barrato
|U+E013|\</del&gt;|Contrassegna la fine di contenuto barrato
|U+E016|\<sub>|Contrassegna l'inizio di contenuto formattato come pedice
|U+E017|\</sub&gt;|Contrassegna la fine di contenuto formattato come pedice
|U+E018|\<sup&gt;|Contrassegna l'inizio di contenuto formattato come apice
|U+E019|\</sup>|Contrassegna la fine di contenuto formattato come apice

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Ricerca Web Bing](overview.md) 
* [Ridimensionare e ritagliare le anteprime](resize-and-crop-thumbnails.md)