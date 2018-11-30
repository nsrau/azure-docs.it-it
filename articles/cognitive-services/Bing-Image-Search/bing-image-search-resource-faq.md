---
title: Domande frequenti - API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Questo articolo offre risposte alle domande frequenti su concetti, codice e scenari correlati all'API Ricerca immagini Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: troubleshooting
ms.date: 10/06/2017
ms.author: aahi
ms.openlocfilehash: ab67769aee5347fdf21a58c6af5b9af02b4f36ba
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161226"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Domande frequenti sull'API Ricerca immagini Bing

Questo articolo offre risposte alle domande frequenti sui concetti, il codice e gli scenari correlati all'API Ricerca immagini Bing per Servizi cognitivi Microsoft in Azure.

## <a name="response-headers-in-javascript"></a>Intestazioni di risposta in JavaScript

Le intestazioni seguenti possono essere incluse nelle risposte dell'API Ricerca immagini Bing.

|||
|-|-|
|`X-MSEdge-ClientID`|ID univoco assegnato da Bing all'utente|
|`BingAPIs-Market`|Mercato usato per soddisfare la richiesta|
|`BingAPIs-TraceId`|Voce di registro nel server API Bing per questa richiesta (ai fini del supporto)|

È particolarmente importante rendere permanente l'ID client e restituirlo con le richieste successive. In questo modo la ricerca userà il contesto passato per classificare i risultati della ricerca e fornirà anche un'esperienza utente coerente.

Tuttavia, quando si chiama l'API Ricerca immagini Bing da JavaScript, le funzionalità di sicurezza predefinite del browser potrebbero impedire l'accesso ai valori di queste intestazioni.

Per poter accedere alle intestazioni, è possibile effettuare la richiesta dell'API Ricerca immagini Bing tramite un proxy CORS. La risposta da un proxy di questo tipo ha un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni di risposta in un elenco elementi consentiti e le rende disponibili a JavaScript.

Si può installare facilmente un proxy CORS per consentire all'[app dell'esercitazione](tutorial-bing-image-search-single-page-app.md) di accedere alle intestazioni client facoltative. Per prima cosa [installare Node.js](https://nodejs.org/en/download/), se non è già disponibile. Immettere quindi il comando seguente al prompt dei comandi.

    npm install -g cors-proxy-server

Modificare quindi l'endpoint dell'API Ricerca immagini Bing nel file HTML come segue:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Infine avviare il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione espandibile delle intestazioni HTTP sotto i risultati della ricerca viene ora visualizzata, tra le altre, l'intestazione `X-MSEdge-ClientID`. Verificare che sia la stessa per ogni richiesta.

## <a name="response-headers-in-production"></a>Intestazioni di risposta in produzione

L'approccio del proxy CORS descritto nella risposta precedente è appropriato per i contesti di sviluppo, testing e apprendimento.

In un ambiente di produzione, tuttavia, occorre ospitare uno script lato server nello stesso dominio della pagina Web che usa l'API Ricerca Web Bing. Questo script dovrebbe effettivamente eseguire le chiamate API su richiesta dal codice JavaScript della pagina Web e restituire tutti i risultati, incluse le intestazioni, al client. Poiché le due risorse (pagina e script) condividono un'origine, CORS non entra in gioco e le intestazioni speciali sono accessibili al codice JavaScript nella pagina Web.

Questo approccio protegge inoltre la chiave API dall'esposizione al pubblico, in quanto è necessaria solo allo script lato server. Lo script può usare un altro metodo (come il referrer HTTP) per verificare che la richiesta sia autorizzata.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? È possibile richiederla o votarla nel [sito Web UserVoice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Vedere anche 

 [Stack Overflow: Servizi cognitivi](http://stackoverflow.com/questions/tagged/bing-api)
