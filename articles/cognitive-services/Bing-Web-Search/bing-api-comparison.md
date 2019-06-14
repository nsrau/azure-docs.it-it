---
title: Informazioni sulle API di ricerca Bing
titleSuffix: Azure Cognitive Services
description: Usare questo articolo per scoprire di più sulle API di ricerca Bing e come è possibile abilitare ricerche cognitive su Internet nelle app e nei servizi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61431362"
---
# <a name="what-are-the-bing-search-apis"></a>Informazioni sulle API di ricerca Bing

API di ricerca Bing consentono di creare App web connesse e i servizi che ricerca le pagine Web, immagini, notizie, posizioni e molto altro senza annunci. Inviando richieste di ricerca tramite le API REST o gli SDK di Ricerca Bing, è possibile ottenere informazioni e contenuti rilevanti per le ricerche Web. Usare questo articolo per apprendere le diverse API di ricerca Bing e come è possibile integrare le ricerche cognitive nelle applicazioni e servizi. I prezzi e i limiti di velocità possono variare tra le API.

## <a name="the-bing-web-search-api"></a>API Ricerca Web Bing

L'[API Ricerca Web Bing](../Bing-Web-Search/index.yml) restituisce pagine Web, immagini, video, notizie e altro ancora. È possibile filtrare le query di ricerca inviate a questa API per includere o escludere determinati tipi di contenuto.

È consigliabile usare l'API Ricerca Web Bing nelle applicazioni che potrebbero dover eseguire la ricerca di tutti i tipi di contenuto Web pertinente. Se l'applicazione effettua la ricerca di un tipo specifico di contenuto online, prendere in considerazione una delle API di ricerca indicate di seguito:

## <a name="content-specific-bing-search-apis"></a>API di ricerca Bing specifiche del contenuto

L'API di ricerca Bing seguenti restituiscono contenuto specifico dal web, ad esempio immagini, notizie, attività commerciali locali e i video.

| API Bing | Descrizione |
| -- | -- |
| [Ricerca entità](../Bing-Entities-Search/index.yml) | L'API Ricerca entità Bing restituisce risultati della ricerca contenenti entità, che possono essere persone, luoghi o cose. A seconda della query, l'API restituirà una o più entità che soddisfano la query di ricerca. La query di ricerca può includere singoli degno di nota, attività commerciali locali, riferimenti, destinazioni e altro ancora. |
| [Ricerca immagini](../Bing-Image-Search/index.yml) | L'API ricerca immagini Bing consente di cercare e trovare statico che quello animato immagini di alta qualità simile a [Bing.com/images](https://www.Bing.com/images). È possibile ridefinire le ricerche in modo da includere o escludere immagini in base ad attributi, come dimensioni, colore, licenza e aggiornamento. È anche possibile eseguire la ricerca di immagini di tendenza, caricare immagini per ottenere informazioni dettagliate e visualizzare anteprime. |
| [Ricerca notizie](../Bing-News-Search/index.yml) | L'API ricerca notizie Bing consente di trovare simile alla storie [Bing.com/news](https://www.Bing.com/news). L'API restituisce articoli di notizie da più fonti o domini specifici. È possibile eseguire ricerche in categorie diverse per ottenere articoli di tendenza, storie principali e titoli. |
| [Ricerca video](../Bing-Video-Search/index.yml) | L'API ricerca Video Bing consente di trovare video nel Web. Si possono ottenere video di tendenza, contenuto correlato e anteprime. |
| [Ricerca visiva](../Bing-visual-search/index.yml) | Caricare un'immagine o usare un URL per ottenere informazioni significative, ad esempio prodotti simili a livello visivo, immagini e ricerche correlate. |
 [Ricerca aziende locali](../bing-local-business-search/index.yml) | L'API di ricerca Bing Business locale consente alle applicazioni di trovare informazioni di contatto e la posizione di attività commerciali locali basate su query di ricerca. |

## <a name="the-bing-custom-search-api"></a>API Ricerca personalizzata Bing

Creazione di un'istanza di ricerca personalizzata con il [ricerca personalizzata Bing](../Bing-Custom-Search/index.yml) API consente di creare un'esperienza di ricerca con stato attivo solo sul contenuto e gli argomenti si è interessati. Ad esempio, dopo aver specificato i domini, siti Web e pagine Web specifiche che eseguirà la ricerca Bing, ricerca personalizzata Bing personalizzino i risultati di tale contenuto specifico. È possibile incorporare le API Suggerimenti automatici, Ricerca immagini e Ricerca video Bing personalizzate per personalizzare ulteriormente la ricerca.

## <a name="additional-bing-search-apis"></a>API di ricerca Bing aggiuntive

Le API di ricerca Bing seguenti consentono di migliorare l'esperienza di ricerca combinandoli con altre API di ricerca di Bing.

| API | Descrizione |
| -- | -- |
| [Suggerimenti automatici Bing](../Bing-Autosuggest/index.yml) | Miglioramento dell'esperienza di ricerca dell'applicazione con l'API suggerimenti automatici Bing, restituendo ricerche suggerite in tempo reale.  |
| [Statistiche Bing](bing-web-stats.md) | Statistiche Bing offre analisi per le API di ricerca Bing usate dall'applicazione. Tra i dati di analisi disponibili sono inclusi il volume delle chiamate, le principali stringhe di query e la distribuzione geografica. |

## <a name="next-steps"></a>Passaggi successivi

* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) dell'API di ricerca Bing
* I [requisiti per l'uso e la visualizzazione di Bing](./use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
