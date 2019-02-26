---
title: Informazioni sui Suggerimenti automatici Bing
titlesuffix: Azure Cognitive Services
description: Imparare a usare l'API Suggerimenti automatici Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: c7ac631ded5d781b2d2949d65f6197e194521055
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268924"
---
# <a name="what-is-bing-autosuggest"></a>Informazioni sui Suggerimenti automatici Bing

Se l'applicazione invia query a una delle API Ricerca Bing, è possibile usare l'API Suggerimenti automatici Bing per migliorare l'esperienza di ricerca degli utenti. L'API Suggerimenti automatici Bing restituisce un elenco di query suggerite basate sulla stringa di query parziale immessa nella casella di ricerca. Quando i caratteri vengono inseriti nella casella di ricerca, è possibile visualizzare i suggerimenti in un elenco a discesa.

## <a name="bing-autosuggest-api-features"></a>Funzionalità dell'API Suggerimenti automatici Bing

| Funzionalità                                                                                                                                                                                 | DESCRIZIONE                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggerimento dei termini di ricerca in tempo reale](concepts/get-suggestions.md) | È possibile migliorare l'esperienza dell'app con l'API Suggerimenti automatici per visualizzare suggerimenti sui termini di ricerca durante la digitazione. |

## <a name="workflow"></a>Flusso di lavoro

L'API Suggerimenti automatici Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. 

1. Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.
2. Inviare una richiesta a questa API ogni volta che un utente digita un nuovo carattere nella casella di ricerca dell'applicazione.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

Questa API viene generalmente chiamata ogni volta che l'utente digita un nuovo carattere nella casella di ricerca dell'applicazione. Man mano che vengono inseriti più caratteri, l'API restituirà più query di ricerca suggerite pertinenti. Ad esempio, i suggerimenti che l'API può restituire per una singola `s` sono probabilmente meno pertinenti di quelli restituiti dalla query per `sail`.

L'esempio seguente mostra una casella di ricerca a discesa con i termini di query suggeriti dall'API Suggerimenti automatici Bing.

![Elenco di casella di ricerca con elenco a discesa di suggerimenti automatici](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando un utente seleziona un suggerimento dall'elenco a discesa, è possibile usare il suggerimento per iniziare la ricerca con una delle API Ricerca Bing o passare direttamente alla pagina dei risultati di ricerca di Bing.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente con la prima richiesta, vedere [Effettuare la prima query](quickstarts/csharp.md).

Acquisire familiarità con la documentazione di riferimento sull'[API Suggerimenti automatici Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). La documentazione di riferimento contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i termini di query suggeriti e le definizioni degli oggetti della risposta.

Informazioni su come effettuare ricerche sul Web con l'[API Ricerca Web Bing](../bing-web-search/search-the-web.md).

Per non violare nessuna delle regole relative all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Requisiti per l'uso e la visualizzazione di Bing).
