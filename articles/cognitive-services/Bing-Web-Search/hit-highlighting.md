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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854032"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Uso degli indicatori di effetto per evidenziare il testo

Bing supporta l'evidenziazione dei riscontri, che contrassegna i termini di query (o altri termini ritenuti rilevanti da Bing) nelle stringhe di visualizzazione di alcune risposte. Ad esempio, i campi, `name` `displayUrl`e `snippet` del risultato della pagina Web possono contenere termini di query contrassegnati. 

Per impostazione predefinita, Bing non include i marcatori di evidenziazione nelle stringhe visualizzate. Per abilitare i marcatori, includere `textDecorations` il parametro di query nella richiesta e impostarlo `true`su.

## <a name="hit-highlighting-example"></a>Esempio di hit highlighting

Nell'esempio seguente viene illustrato un risultato Web `Sailing Dinghy`per. Bing ha contrassegnato l'inizio e la fine del termine della query usando i caratteri Unicode E000 e E001.
  
![Evidenziazione dei risultati](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Prima di visualizzare il risultato nell'interfaccia utente, sostituire i caratteri Unicode con quelli appropriati per il formato di visualizzazione.

## <a name="marker-formatting"></a>Formattazione marcatore

Bing fornisce la possibilità di usare i caratteri Unicode o i tag HTML come marcatori. Per specificare i marcatori da usare, includere il parametro di query [textFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat): 

| Value             | Marcatore                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caratteri Unicode (impostazione predefinita) |
| `textFormat=HTML` | Caratteri HTML              |

## <a name="additional-text-decorations"></a>Decorazioni di testo aggiuntive

Bing può restituire diverse decorazioni di testo. Una `Computation` risposta, ad esempio, può contenere marcatori di pedice per `log(2)` il termine `expression` di query nel campo.

![Marcatori in computation](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se la richiesta non specifica le decorazioni, il `expression` campo conterrà `log10(2)`. 

Se `textDecorations` è`true`, Bing può includere i marcatori seguenti nelle stringhe di visualizzazione delle risposte. Se non è presente alcun tag HTML equivalente, la cella della tabella è vuota.

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

* [Qual è la API Ricerca Web Bing?](overview.md) 
* [Ridimensionare e ritagliare le anteprime](resize-and-crop-thumbnails.md)