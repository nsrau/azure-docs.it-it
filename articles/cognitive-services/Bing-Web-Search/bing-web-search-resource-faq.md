---
title: Domande frequenti sull'API Ricerca Web Bing di Azure | Microsoft Docs
description: Risposte a domande comuni sull'API Ricerca Web Bing di Servizi cognitivi di Azure in Azure.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373124"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>Domande frequenti sull'API Ricerca Web Bing (Servizi cognitivi)
 
 Questo articolo offre risposte alle domande frequenti sui concetti, il codice e gli scenari correlati all'API Ricerca Web Bing per Servizi cognitivi Microsoft in Azure.

## <a name="response-headers-in-javascript"></a>Intestazioni di risposta in JavaScript

Le intestazioni seguenti possono essere incluse nelle risposte dell'API Ricerca Web Bing.

|||
|-|-|
|`X-MSEdge-ClientID`|ID univoco assegnato da Bing all'utente|
|`BingAPIs-Market`|Mercato usato per soddisfare la richiesta|
|`BingAPIs-TraceId`|Voce di registro nel server API Bing per questa richiesta (ai fini del supporto)|

È particolarmente importante rendere permanente l'ID client e restituirlo con le richieste successive. In questo modo la ricerca userà il contesto passato per classificare i risultati della ricerca e fornirà anche un'esperienza utente coerente.

Tuttavia, quando si chiama l'API Ricerca Web Bing da JavaScript, le funzionalità di sicurezza predefinite del browser potrebbero impedire l'accesso ai valori di queste intestazioni.

Per poter accedere alle intestazioni, è possibile effettuare la richiesta dell'API Ricerca Web Bing tramite un proxy CORS. La risposta da un proxy di questo tipo ha un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni di risposta in un elenco elementi consentiti e le rende disponibili a JavaScript.

Si può installare facilmente un proxy CORS per consentire all'[app dell'esercitazione](tutorial-bing-web-search-single-page-app.md) di accedere alle intestazioni client facoltative. Per prima cosa [installare Node.js](https://nodejs.org/en/download/), se non è già disponibile. Immettere quindi il comando seguente al prompt dei comandi.

    npm install -g cors-proxy-server

Modificare quindi l'endpoint dell'API Ricerca Web Bing nel file HTML in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Infine avviare il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione espandibile delle intestazioni HTTP sotto i risultati della ricerca viene ora visualizzata, tra le altre, l'intestazione `X-MSEdge-ClientID`. Verificare che sia la stessa per ogni richiesta.

## <a name="response-headers-in-production"></a>Intestazioni di risposta in produzione

L'approccio del proxy CORS descritto nella risposta precedente è appropriato per i contesti di sviluppo, testing e apprendimento. 

In un ambiente di produzione, tuttavia, occorre ospitare uno script lato server nello stesso dominio della pagina Web che usa l'API Ricerca Web Bing. Questo script dovrebbe effettivamente eseguire le chiamate API su richiesta dal codice JavaScript della pagina Web e restituire tutti i risultati, incluse le intestazioni, al client. Poiché le due risorse (pagina e script) condividono un'origine, CORS non entra in gioco e le intestazioni speciali sono accessibili al codice JavaScript nella pagina Web. 

Questo approccio protegge inoltre la chiave API dall'esposizione al pubblico, in quanto è necessaria solo allo script lato server. Lo script può usare un altro metodo per verificare che la richiesta sia autorizzata.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? È possibile richiederla o votarla nel [sito Web UserVoice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Vedere anche 

 [Stack Overflow: Servizi cognitivi](http://stackoverflow.com/questions/tagged/bing-api)