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
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 60de69c71df61cb53e446aa8759842f72c2acf5d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864180"
---
# <a name="what-are-the-bing-search-apis"></a>Informazioni sulle API di ricerca Bing

Le API di ricerca Bing consentono di creare app e servizi connessi al Web per la ricerca di pagine Web, immagini, notizie, luoghi e molto altro senza annunci. Inviando richieste di ricerca tramite le API REST o gli SDK di Ricerca Bing, è possibile ottenere informazioni e contenuti rilevanti per le ricerche Web. Usare questo articolo per scoprire di più sulle varie API di ricerca Bing e su come è possibile integrare ricerche cognitive in applicazioni e servizi. I prezzi e i limiti di velocità possono variare tra le API.

## <a name="the-bing-web-search-api"></a>API Ricerca Web Bing

L'[API Ricerca Web Bing](../Bing-Web-Search/index.yml) restituisce pagine Web, immagini, video, notizie e altro ancora. Le query di ricerca inviate a questa API possono essere filtrate per includere o escludere determinati tipi di contenuto.

È consigliabile usare l'API Ricerca Web Bing nelle applicazioni che potrebbero dover eseguire la ricerca di tutti i tipi di contenuto Web pertinente. Se l'applicazione effettua la ricerca di un tipo specifico di contenuto online, prendere in considerazione una delle API di ricerca indicate di seguito: 

## <a name="content-specific-bing-search-apis"></a>API di ricerca Bing specifiche del contenuto

Le API di ricerca Bing seguenti restituiscono contenuto specifico dal Web, ad esempio immagini, notizie, aziende locali e video.

| API Bing | DESCRIZIONE |
| -- | -- | 
| [Ricerca entità](../Bing-Entities-Search/index.yml) | L'API Ricerca entità Bing restituisce risultati della ricerca contenenti entità, che possono essere persone, luoghi o cose. A seconda della query, l'API restituirà una o più entità che soddisfano la query di ricerca, che possono includere celebrità, aziende locali, luoghi di interesse, destinazioni e altro ancora. |
| [Ricerca immagini](../Bing-Image-Search/index.yml) | L'API Ricerca immagini Bing consente di cercare e trovare immagini statiche e animate di alta qualità, in modo simile a [Bing.com/images](https://www.Bing.com/images). È possibile ridefinire le ricerche in modo da includere o escludere immagini in base ad attributi, come dimensioni, colore, licenza e aggiornamento. È anche possibile eseguire la ricerca di immagini di tendenza, caricare immagini per ottenere informazioni dettagliate e visualizzare anteprime. |
| [Ricerca notizie](../Bing-News-Search/index.yml) | L'API Ricerca notizie Bing consente di trovare notizie, in modo simile a [Bing.com/news](https://www.Bing.com/news). L'API restituisce articoli di notizie da più fonti o domini specifici. È possibile eseguire ricerche in categorie diverse per ottenere articoli di tendenza, storie principali e titoli.
| [Ricerca video](../Bing-Video-Search/index.yml) | L'API Ricerca video Bing consente di trovare video nel Web. Si possono ottenere video di tendenza, contenuto correlato e anteprime. |
| [Ricerca visiva](../Bing-visual-search/index.yml) | Caricare un'immagine o usare un URL per ottenere informazioni significative, ad esempio prodotti simili a livello visivo, immagini e ricerche correlate. |
 [Ricerca aziende locali](../bing-local-business-search/index.yml) | L'API Bing Local Business Search consente alle applicazioni di trovare informazioni di contatto e sulla posizione di aziende locali in base alle query di ricerca. |

## <a name="the-bing-custom-search-api"></a>API Ricerca personalizzata Bing

La creazione di un'istanza di ricerca personalizzata con l'API [Ricerca personalizzata Bing](../Bing-Custom-Search/index.yml) consente di creare un'esperienza di ricerca incentrata solo sul contenuto o gli argomenti di interesse. Ad esempio, dopo aver specificato i domini, i siti Web e le pagine Web specifiche in cui Bing eseguirà la ricerca, i risultati verranno personalizzati per tale contenuto specifico. È possibile incorporare le API Suggerimenti automatici, Ricerca immagini e Ricerca video Bing personalizzate per personalizzare ulteriormente la ricerca.  

## <a name="additional-bing-search-apis"></a>API di ricerca Bing aggiuntive

Le API di ricerca Bing seguenti consentono di migliorare l'esperienza di ricerca combinandole con altre API di ricerca Bing.

| API | DESCRIZIONE |
| -- | -- | 
| [Suggerimenti automatici Bing](../Bing-Autosuggest/index.yml) | È possibile migliorare l'esperienza di ricerca dell'applicazione con l'API Suggerimenti automatici Bing, restituendo ricerche suggerite in tempo reale.  |
| [Statistiche Bing](bing-web-stats.md) | Statistiche Bing offre analisi per le API di ricerca Bing usate dall'applicazione. Tra i dati di analisi disponibili sono inclusi il volume delle chiamate, le principali stringhe di query e la distribuzione geografica. |

## <a name="next-steps"></a>Passaggi successivi

* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) dell'API di ricerca Bing
* I [requisiti per l'uso e la visualizzazione di Bing](./use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
