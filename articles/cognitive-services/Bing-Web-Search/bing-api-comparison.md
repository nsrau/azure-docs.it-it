---
title: Informazioni sulle API di ricerca Bing
titleSuffix: Azure Cognitive Services
description: Usare questo articolo per scoprire di più sulle API di ricerca Bing e come è possibile abilitare ricerche cognitive su Internet nelle app e nei servizi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775541"
---
# <a name="what-are-the-bing-search-apis"></a>Informazioni sulle API di ricerca Bing

Il API di ricerca Bing consente di creare app e servizi connessi al Web in grado di trovare pagine Web, immagini, notizie, posizioni e altro ancora senza annunci. Inviando richieste di ricerca tramite le API REST o gli SDK di Ricerca Bing, è possibile ottenere informazioni e contenuti rilevanti per le ricerche Web. Usare questo articolo per ottenere informazioni sulle diverse API di ricerca Bing e su come integrare le ricerche cognitive nelle applicazioni e nei servizi. I prezzi e i limiti di velocità possono variare tra le API.

## <a name="the-bing-web-search-api"></a>API Ricerca Web Bing

L'[API Ricerca Web Bing](../Bing-Web-Search/overview.md) restituisce pagine Web, immagini, video, notizie e altro ancora. È possibile filtrare le query di ricerca inviate a questa API per includere o escludere determinati tipi di contenuto.

È consigliabile usare l'API Ricerca Web Bing nelle applicazioni che potrebbero dover eseguire la ricerca di tutti i tipi di contenuto Web pertinente. Se l'applicazione effettua la ricerca di un tipo specifico di contenuto online, prendere in considerazione una delle API di ricerca indicate di seguito:

## <a name="content-specific-bing-search-apis"></a>API di ricerca Bing specifiche del contenuto

Le API di ricerca Bing seguenti restituiscono contenuti specifici dal Web, ad esempio immagini, notizie, aziende locali e video.

| API Bing | Description |
| -- | -- |
| [Ricerca entità](../Bing-Entities-Search/overview.md) | L'API Ricerca entità Bing restituisce risultati della ricerca contenenti entità, che possono essere persone, luoghi o cose. A seconda della query, l'API restituirà una o più entità che soddisfano la query di ricerca. La query di ricerca può includere utenti degni di nota, aziende locali, punti di interesse, destinazioni e altro ancora. |
| [Ricerca immagini](../Bing-Image-Search/overview.md) | Il API Ricerca immagini Bing consente di cercare e trovare immagini statiche e animate di alta qualità simili a [Bing.com/images](https://www.Bing.com/images). È possibile ridefinire le ricerche in modo da includere o escludere immagini in base ad attributi, come dimensioni, colore, licenza e aggiornamento. È anche possibile eseguire la ricerca di immagini di tendenza, caricare immagini per ottenere informazioni dettagliate e visualizzare anteprime. |
| [Ricerca notizie](../Bing-News-Search/search-the-web.md) | Il API Ricerca notizie Bing consente di trovare storie di notizie simili a [Bing.com/News](https://www.Bing.com/news). L'API restituisce articoli di notizie da più fonti o domini specifici. È possibile eseguire ricerche in categorie diverse per ottenere articoli di tendenza, storie principali e titoli. |
| [Ricerca video](../Bing-Video-Search/overview.md) | Il API Ricerca video Bing consente di trovare i video sul Web. Si possono ottenere video di tendenza, contenuto correlato e anteprime. |
| [Ricerca visiva](../Bing-visual-search/overview.md) | Caricare un'immagine o usare un URL per ottenere informazioni significative, ad esempio prodotti simili a livello visivo, immagini e ricerche correlate. |
 [Ricerca aziende locali](../bing-local-business-search/overview.md) | L'API di ricerca aziendale locale Bing consente alle applicazioni di trovare informazioni sul contatto e sul percorso relative alle aziende locali in base alle query di ricerca. |

## <a name="the-bing-custom-search-api"></a>API Ricerca personalizzata Bing

La creazione di un'istanza di ricerca personalizzata con l'API [ricerca personalizzata Bing](../Bing-Custom-Search/overview.md) consente di creare un'esperienza di ricerca incentrata solo sul contenuto e sugli argomenti a cui si è interessati. Ad esempio, dopo aver specificato i domini, i siti Web e le pagine Web specifiche che Bing cercherà, Ricerca personalizzata Bing utilizzerà i risultati in base a tale contenuto specifico. È possibile incorporare le API Suggerimenti automatici, Ricerca immagini e Ricerca video Bing personalizzate per personalizzare ulteriormente la ricerca.

## <a name="additional-bing-search-apis"></a>API di ricerca Bing aggiuntive

Il API di ricerca Bing seguente consente di migliorare l'esperienza di ricerca unendoli con altre API di ricerca Bing.

| API SmartBear Ready! | Description |
| -- | -- |
| [Suggerimenti automatici Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Migliorare l'esperienza di ricerca dell'applicazione con la API Suggerimenti automatici Bing restituendo le ricerche suggerite in tempo reale.  |
| [Statistiche Bing](bing-web-stats.md) | Statistiche Bing offre analisi per le API di ricerca Bing usate dall'applicazione. Tra i dati di analisi disponibili sono inclusi il volume delle chiamate, le principali stringhe di query e la distribuzione geografica. |

## <a name="next-steps"></a>Passaggi successivi

* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) dell'API di ricerca Bing
* I [requisiti per l'uso e la visualizzazione di Bing](./use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
