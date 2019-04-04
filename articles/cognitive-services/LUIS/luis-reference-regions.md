---
title: Regioni di pubblicazione ed endpoint
titleSuffix: Azure Cognitive Services
description: 3 Creazione di aree e i rispettivi portali supportano tutte le aree di pubblicazione molti. La regione in cui si pubblica l'app LUIS corrisponde alla regione o alla posizione specificata nel portale di Azure quando si crea una chiave endpoint LUIS di Azure. Quando si pubblica un'app, LUIS genera automaticamente un URL endpoint per la regione associata alla chiave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891413"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Creazione e pubblicazione di aree e chiavi associate

Tre aree di creazione e modifica sono supportate da portali LUIS corrispondenti. Per pubblicare un'app LUIS in più regioni, è necessaria almeno una chiave per regione. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS di creazione di aree
Sono disponibili tre portali di creazione e modifica LUIS, basati sull'area. La creazione e la pubblicazione devono avvenire nella stessa regione. 

|LUIS|Regione di creazione|Nome area di Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Dati<br>non Europa<br>non Australia| `westus`|
|[au.luis.ai][au.luis.ai]|Australia| `australiaeast`|
|[au.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Creazione e modifica le aree hanno [failover aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Pubblicazione di aree e le risorse di Azure
L'app è pubblicata in tutte le regioni associate alle risorse LUIS aggiunte nel portale di LUIS. Ad esempio, per un'app creata in [www.luis.ai][www.luis.ai], se si crea una risorsa di servizi cognitivi o LUIS in **westus** e [aggiungerla all'app come una risorsa ](luis-how-to-azure-subscription.md), la pubblicazione dell'app in tale area. 

## <a name="public-apps"></a>App pubbliche
Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la propria chiave di risorsa.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Aree di pubblicazione sono associate alla creazione di aree

L'app della regione di creazione può essere pubblicata solo in una regione di pubblicazione corrispondente. Se l'app si trova nella regione di creazione errata, esportarla e importarla nella regione di creazione corretta per la regione di pubblicazione. 

Le app LUIS create in https://www.luis.ai possono essere pubblicate in tutti gli endpoint, ad eccezione delle regioni [europea](#publishing-to-europe) e [australiana](#publishing-to-australia). 

## <a name="publishing-to-europe"></a>Pubblicazione in Europa

Per pubblicare nelle regioni europee, creare le app LUIS solo in https://eu.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione europea, viene visualizzato un messaggio di avviso. Usare invece https://eu.luis.ai. Le app LUIS create in [https://eu.luis.ai][eu.luis.ai] non vengono migrate automaticamente in altre regioni. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="europe-publishing-regions"></a>Pubblicazione aree in Europa

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[au.luis.ai][eu.luis.ai]| Francia centrale<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[au.luis.ai][eu.luis.ai]| Europa settentrionale<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[au.luis.ai][eu.luis.ai]| Europa occidentale<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[au.luis.ai][eu.luis.ai]| Regno Unito meridionale<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Pubblicazione in Australia

Per pubblicare nelle regioni australiane, creare le app LUIS solo in https://au.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione australiana, viene visualizzato un messaggio di avviso. Usare invece https://au.luis.ai. Le app LUIS create in [https://au.luis.ai][au.luis.ai] non vengono migrate automaticamente in altre regioni. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="australia-publishing-regions"></a>Aree dell'Australia pubblicazione

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australia orientale<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Pubblicazione in altre aree

Per pubblicare in altre aree, si creano LUIS le App in [ https://www.luis.ai ](https://www.luis.ai) solo. 

## <a name="other-publishing-regions"></a>Altre aree di pubblicazione

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| India centrale<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia orientale<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone orientale<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone occidentale<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Corea del Sud centrale<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia sud-orientale<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Canada centrale<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centrali<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-settentrionali<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-meridionali<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-occidentali<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| America del Sud | `westus`<br>[www.luis.ai][www.luis.ai] | Brasile meridionale<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Endpoint

LUIS è attualmente 2 endpoint: uno per la creazione e uno per l'analisi di stima di query.

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

## <a name="failover-regions"></a>Aree di failover

Ogni area ha un'area secondaria per eseguire il failover. Si verifica un errore Europa over in Europa e in Australia viene eseguito il failover all'interno di Australia.

Creazione e modifica le aree hanno [failover aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Fare riferimento a entità predefinite](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
