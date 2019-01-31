---
title: Domande frequenti sull'API Controllo ortografico Bing
titlesuffix: Azure Cognitive Services
description: Ecco le risposte alle domande comuni sull'API Controllo ortografico Bing in Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 90acc80b19058c2a7eb963f9e423883846519b2c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164929"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Domande frequenti sull'API Controllo ortografico Bing

 Questo articolo offre risposte alle domande frequenti sui concetti, il codice e gli scenari correlati all'API Controllo ortografico Bing per Servizi cognitivi Microsoft in Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Come si ottengono le intestazioni client facoltative quando si chiama l'API Controllo ortografico Bing da JavaScript?

Le intestazioni seguenti sono facoltative, ma è consigliabile considerarle obbligatorie. Queste intestazioni consentono all'API Controllo ortografico Bing di restituire risultati più accurati.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Tuttavia, quando si chiama l'API Controllo ortografico Bing da JavaScript, le funzionalità di sicurezza predefinite del browser potrebbero impedire l'accesso ai valori di queste intestazioni.

Per risolvere il problema, è possibile inviare la richiesta all'API Controllo ortografico Bing tramite un proxy CORS. La risposta da un proxy di questo tipo contiene un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni della risposta nell'elenco elementi consentiti e le rende disponibili per JavaScript.

Si può installare facilmente un proxy CORS per consentire all'[app dell'esercitazione](tutorials/spellcheck.md) di accedere alle intestazioni client facoltative. Per prima cosa [installare Node.js](https://nodejs.org/en/download/), se non è già disponibile. Immettere quindi il comando seguente al prompt dei comandi.

    npm install -g cors-proxy-server

Modificare quindi l'endpoint dell'API Controllo ortografico Bing nel file HTML come segue:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Infine avviare il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione espandibile delle intestazioni HTTP sotto i risultati della ricerca viene ora visualizzata, tra le altre, l'intestazione `X-MSEdge-ClientID`. Verificare che sia la stessa per ogni richiesta.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? È possibile richiederla o votarla nel [sito Web UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Vedere anche 

 [StackOverflow: Servizi cognitivi](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
