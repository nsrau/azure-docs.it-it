---
title: Regioni ed endpoint Language Understanding (LUIS) - Azure - Servizi cognitivi LUIS | Microsoft Docs
titleSuffix: Azure
description: La regione in cui si pubblica l'app LUIS corrisponde alla regione o alla posizione specificata nel portale di Azure quando si crea una chiave endpoint LUIS di Azure. Quando si pubblica un'app, LUIS genera automaticamente un URL endpoint per la regione associata alla chiave. Per pubblicare un'app LUIS in più regioni, è necessaria almeno una chiave per regione.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 7812ce37cb22c8774c785f5f645b8fef90b02a3e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815504"
---
# <a name="regions-and-keys"></a>Regioni e chiavi

La regione in cui si pubblica l'app LUIS corrisponde alla regione o alla posizione specificata nel portale di Azure quando si crea una chiave endpoint LUIS di Azure. Quando si [pubblica un'app](./luis-how-to-publish-app.md), LUIS genera automaticamente un URL endpoint per la regione associata alla chiave. Per pubblicare un'app LUIS in più regioni, è necessaria almeno una chiave per regione. 

## <a name="luis-website"></a>Sito Web LUIS
Esistono tre siti Web LUIS, in base alla regione. La creazione e la pubblicazione devono avvenire nella stessa regione. 

|LUIS|Region|
|--|--|
|[www.luis.ai][www.luis.ai]|Dati<br>non Europa<br>non Australia|
|[au.luis.ai][au.luis.ai]|Australia|
|[au.luis.ai][eu.luis.ai]|Europa|


## <a name="publishing-regions"></a>Regioni di pubblicazione

Le app LUIS create in https://www.luis.ai possono essere pubblicate in tutti gli endpoint, ad eccezione delle regioni [europea](#publishing-to-europe) e [australiana](#publishing-to-australia). 

L'app della regione di creazione può essere pubblicata solo in una regione di pubblicazione corrispondente. Se l'app si trova nella regione di creazione errata, esportarla e importarla nella regione di creazione corretta per la regione di pubblicazione. 

 Regione globale | Regione di creazione | Regione di pubblicazione e query   |   Sito Web LUIS | Formato URL endpoint   |
|-----|------|------|------|------|
| Asia | Stati Uniti occidentali| Asia orientale     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | Stati Uniti occidentali| Asia sud-orientale     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australia](#publishing-to-australia) | Australia orientale| Australia orientale     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Europa occidentale| Europa settentrionale     | [au.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Europa occidentale| Europa occidentale     | [au.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| America del Nord | Stati Uniti occidentali | Stati Uniti orientali      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | Stati Uniti occidentali | Stati Uniti orientali 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | Stati Uniti occidentali | Stati Uniti centro-meridionali     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| America del Nord | Stati Uniti occidentali | Stati Uniti centro-occidentali     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | Stati Uniti occidentali | Stati Uniti occidentali |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| America del Nord | Stati Uniti occidentali | Stati Uniti occidentali 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| America del Sud | Stati Uniti occidentali | Brasile meridionale     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Pubblicazione in Europa

Per pubblicare nelle regioni europee, creare le app LUIS solo in https://eu.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione europea, viene visualizzato un messaggio di avviso. Usare invece https://eu.luis.ai. Le app LUIS create in [https://eu.luis.ai][eu.luis.ai] non vengono migrate automaticamente in altre regioni. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="publishing-to-australia"></a>Pubblicazione in Australia

Per pubblicare nelle regioni australiane, creare le app LUIS solo in https://au.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione australiana, viene visualizzato un messaggio di avviso. Usare invece https://au.luis.ai. Le app LUIS create in [https://au.luis.ai][au.luis.ai] non vengono migrate automaticamente in altre regioni. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="endpoints"></a>Endpoint

LUIS dispone attualmente di 2 endpoint: uno per la creazione e uno per l'analisi del testo.

|Scopo|URL|
|--|--|
|Creazione|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analisi del testo (query di stima)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

La tabella seguente illustra i parametri indicati con parentesi graffe `{}` nella tabella precedente.

|Parametro|Scopo|
|--|--|
|region|Area di Azure - per la creazione e la pubblicazione sono disponibili aree diverse|
|appID|ID dell'app LUIS usato nella route dell'URL e disponibile nel dashboard dell'app|
|q|testo dell'espressione inviato dall'applicazione client, ad esempio chatbot|


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimento a entità predefinite](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai