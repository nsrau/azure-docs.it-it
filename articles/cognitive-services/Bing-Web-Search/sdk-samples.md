---
title: Esempi di SDK di Ricerca Bing Web
titleSuffix: Azure Cognitive Services
description: Usare l'SDK di ricerca Web Bing per aggiungere funzionalità di ricerca all'applicazione Python, Node.js, C# o Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350433"
---
# <a name="bing-web-search-sdk-samples"></a>Esempi di SDK di Ricerca Bing Web

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

SDK di Ricerca Bing Web è disponibile in Python, Node.js, C# e Java. Esempi di codice, prerequisiti e istruzioni di compilazione sono disponibili su GitHub. Sono supportati gli scenari seguenti:

* Query di una singola parola e stampa di nome e URL del primo risultato per pagina Web, immagine, articolo e video.
* Query di una frase, verifica del numero di risultati e stampa di nome e URL del primo risultato.
* Query in base a un termine di ricerca con filtri di risposta impostati su `news` e stampa dei dettagli dei risultati relativi alle notizie.
* Query in base a un termine di ricerca con i parametri `answerCount` e `promote` e quindi stampa dei dettagli dei risultati.

## <a name="sdks-and-libraries"></a>SDK e raccolte

Usare i collegamenti seguenti per accedere all'SDK per il linguaggio preferito.

* Introduzione agli [esempi Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Vedere anche [Librerie Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) per le definizioni e le dipendenze.
* Introduzione agli [esempi Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Vedere anche la pagina relativa alla [Ricerca Web in Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Introduzione agli [esempi .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Vedere anche [Librerie .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch) per le definizioni e le dipendenze.
* Introduzione agli [esempi Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Vedere anche [Librerie Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) per le definizioni e le dipendenze.