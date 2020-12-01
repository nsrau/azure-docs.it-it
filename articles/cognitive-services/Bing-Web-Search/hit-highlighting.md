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
ms.openlocfilehash: 32c3279cacddf10e77e8d245ba525ab766efd6fd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351879"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Uso degli indicatori di effetto per evidenziare il testo

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing supporta l'evidenziazione dei riscontri, che contrassegna i termini di query (o altri termini ritenuti rilevanti da Bing) nelle stringhe di visualizzazione di alcune risposte. Ad esempio, i campi, e del risultato della pagina Web `name` `displayUrl` `snippet` possono contenere termini di query contrassegnati. 

Per impostazione predefinita, Bing non include i marcatori di evidenziazione nelle stringhe visualizzate. Per abilitare i marcatori, includere il `textDecorations` parametro di query nella richiesta e impostarlo su `true` .

## <a name="hit-highlighting-example"></a>Esempio di hit highlighting

Nell'esempio seguente viene illustrato un risultato Web per `Sailing Dinghy` . Bing ha contrassegnato l'inizio e la fine del termine della query usando i caratteri Unicode E000 e E001.
  
![Evidenziazione dei risultati](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Prima di visualizzare il risultato nell'interfaccia utente, sostituire i caratteri Unicode con quelli appropriati per il formato di visualizzazione.

## <a name="marker-formatting"></a>Formattazione marcatore

Bing fornisce la possibilità di usare i caratteri Unicode o i tag HTML come marcatori. Per specificare i marcatori da usare, includere il [parametro di query](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) TextFormat: 

| Valore             | Marcatore                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caratteri Unicode (impostazione predefinita) |
| `textFormat=HTML` | Caratteri HTML              |

## <a name="additional-text-decorations"></a>Decorazioni di testo aggiuntive

Bing può restituire diverse decorazioni di testo. Una risposta, ad esempio, `Computation` può contenere marcatori di pedice per il termine `log(2)` di query nel `expression` campo.

![Marcatori in computation](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se la richiesta non specifica le decorazioni, il `expression` campo conterrà `log10(2)` . 

Se `textDecorations` è `true` , Bing può includere i marcatori seguenti nelle stringhe di visualizzazione delle risposte. Se non è presente alcun tag HTML equivalente, la cella della tabella è vuota.

|Unicode|HTML|Descrizione
|-|-|-
|U+E000|\<b>|Contrassegna l'inizio del termine di query (evidenziazione dei risultati)
|U+E001|\</b>|Contrassegna la fine del termine di query
|U+E002|\<i>|Contrassegna l'inizio di contenuto in corsivo 
|U+E003|\</i>|Contrassegna la fine di contenuto in corsivo
|U+E004|\<br/>|Contrassegna un'interruzione di riga
|U+E005||Contrassegna l'inizio di un numero di telefono
|U+E006||Contrassegna la fine di un numero di telefono
|U+E007||Contrassegna l'inizio di un indirizzo
|U+E008||Contrassegna la fine di un indirizzo
|U+E009|\&nbsp;|Contrassegna uno spazio unificatore
|U+E00C|\<strong>|Contrassegna l'inizio di contenuto in grassetto
|U+E00D|\</strong>|Contrassegna la fine di contenuto in grassetto
|U+E00E||Contrassegna l'inizio di contenuto con sfondo più chiaro rispetto allo sfondo circostante
|U+E00F||Contrassegna la fine di contenuto con sfondo più chiaro rispetto allo sfondo circostante
|U+E010||Contrassegna l'inizio di contenuto con sfondo più scuro rispetto allo sfondo circostante
|U+E011||Contrassegna la fine di contenuto con sfondo più scuro rispetto allo sfondo circostante
|U+E012|\<del>|Contrassegna l'inizio di contenuto barrato
|U+E013|\</del>|Contrassegna la fine di contenuto barrato
|U+E016|\<sub>|Contrassegna l'inizio di contenuto formattato come pedice
|U+E017|\</sub>|Contrassegna la fine di contenuto formattato come pedice
|U+E018|\<sup>|Contrassegna l'inizio di contenuto formattato come apice
|U+E019|\</sup>|Contrassegna la fine di contenuto formattato come apice

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Ricerca Web Bing](overview.md) 
* [Ridimensionare e ritagliare le anteprime](resize-and-crop-thumbnails.md)