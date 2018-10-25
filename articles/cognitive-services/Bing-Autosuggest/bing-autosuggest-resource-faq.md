---
title: Domande frequenti (FAQ) - API Suggerimenti automatici Bing
titlesuffix: Azure Cognitive Services
description: Informazioni su come ottenere risposte alle domande comuni sull'API Suggerimenti automatici Bing.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 84f1b0555922119e9de4addc3d51ac233e7bae65
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831365"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Domande frequenti (FAQ) sull'API Suggerimenti automatici Bing
 
 Questo articolo offre risposte alle domande frequenti sui concetti, il codice e gli scenari correlati all'API Suggerimenti automatici per Servizi cognitivi di Azure.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Come si ottengono le intestazioni client facoltative quando si chiama l'API Suggerimenti automatici Bing da JavaScript?

Le intestazioni seguenti sono facoltative, ma è consigliabile considerarle obbligatorie. Queste intestazioni consentono all'API Suggerimenti automatici Bing di restituire risultati più accurati.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Quando si chiama l'API Suggerimenti automatici Bing da JavaScript, tuttavia, le funzionalità di sicurezza predefinite del browser potrebbero impedire l'accesso ai valori di queste intestazioni.

Per risolvere il problema, è possibile inviare la richiesta all'API Suggerimenti automatici Bing tramite un proxy CORS. La risposta da un proxy di questo tipo contiene un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni della risposta nell'elenco elementi consentiti e le rende disponibili per JavaScript.

Si può installare facilmente un proxy CORS per consentire all'[app dell'esercitazione](tutorials/autosuggest.md) di accedere alle intestazioni client facoltative. Per prima cosa [installare Node.js](https://nodejs.org/en/download/), se non è già disponibile. Immettere quindi il comando seguente al prompt dei comandi.

    npm install -g cors-proxy-server

Modificare quindi l'endpoint dell'API Suggerimenti automatici Bing nel file HTML come segue:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Avviare infine il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione Intestazioni HTTP espandibile sotto i risultati della ricerca è ora possibile vedere l'intestazione `X-MSEdge-ClientID`, tra le altre, e verificare che sia la stessa per ogni richiesta.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? È possibile richiederla o votarla nel [sito Web UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Vedere anche 

- [Stack Overflow: Servizi cognitivi](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
