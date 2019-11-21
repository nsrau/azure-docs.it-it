---
title: Publishing regions & endpoints - LUIS
titleSuffix: Azure Cognitive Services
description: The region specified in the Azure portal is the same where you will publish the LUIS app and an endpoint URL is generated for this same region.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 4f8e5d1c5ac3b0383fee5a901d2bb34edab8dae2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225483"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Creazione e pubblicazione di aree e chiavi associate

Three authoring regions are supported by corresponding LUIS portals. Per pubblicare un'app LUIS in più regioni, è necessaria almeno una chiave per regione. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS Authoring regions
There are three LUIS authoring portals, based on region. La creazione e la pubblicazione devono avvenire nella stessa regione. 

|LUIS|Regione di creazione|Azure region name|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|Altre isole<br>non Europa<br>non Australia| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Australia| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publishing regions and Azure resources
L'app è pubblicata in tutte le regioni associate alle risorse LUIS aggiunte nel portale di LUIS. For example, for an app created on [www.luis.ai][www.luis.ai], if you create a LUIS or Cognitive Service resource in **westus** and [add it to the app as a resource](luis-how-to-azure-subscription.md), the app is published in that region. 

## <a name="public-apps"></a>App pubbliche
Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la propria chiave di risorsa.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publishing regions are tied to authoring regions

L'app della regione di creazione può essere pubblicata solo in una regione di pubblicazione corrispondente. Se l'app si trova nella regione di creazione errata, esportarla e importarla nella regione di creazione corretta per la regione di pubblicazione. 

Le app LUIS create in https://www.luis.ai possono essere pubblicate in tutti gli endpoint, ad eccezione delle regioni [europea](#publishing-to-europe) e [australiana](#publishing-to-australia). 

## <a name="publishing-to-europe"></a>Pubblicazione in Europa

Per pubblicare nelle regioni europee, creare le app LUIS solo in https://eu.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione europea, viene visualizzato un messaggio di avviso. Usare invece https://eu.luis.ai. LUIS apps created at [https://eu.luis.ai][eu.luis.ai] don't automatically migrate to other regions. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="europe-publishing-regions"></a>Europe publishing regions

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francia centrale<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa settentrionale<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa occidentale<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Regno Unito meridionale<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Pubblicazione in Australia

Per pubblicare nelle regioni australiane, creare le app LUIS solo in https://au.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione australiana, viene visualizzato un messaggio di avviso. Usare invece https://au.luis.ai. LUIS apps created at [https://au.luis.ai][au.luis.ai] don't automatically migrate to other regions. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="australia-publishing-regions"></a>Australia publishing regions

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australia orientale<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publishing to other regions

To publish to the other regions, you create LUIS apps at [https://www.luis.ai](https://www.luis.ai) only. 

## <a name="other-publishing-regions"></a>Other publishing regions

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| Africa | `westus`<br>[www.luis.ai][www.luis.ai]| Sudafrica settentrionale<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| India centrale<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia orientale<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone orientale<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone occidentale<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Corea centrale<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia sud-orientale<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Canada centrale<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centrali<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti Orientali<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-settentrionali<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-meridionali<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-occidentali<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| America del Sud | `westus`<br>[www.luis.ai][www.luis.ai] | Brasile meridionale<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Endpoint

LUIS currently has 2 endpoints: one for authoring and one for query prediction analysis.

|Finalità|URL|
|--|--|
|Creazione|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analisi del testo (query di stima)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

La tabella seguente illustra i parametri indicati con parentesi graffe `{}` nella tabella precedente.

|Parametro|Finalità|
|--|--|
|region|Area di Azure - per la creazione e la pubblicazione sono disponibili aree diverse|
|appID|ID dell'app LUIS usato nella route dell'URL e disponibile nel dashboard dell'app|
|q|testo dell'espressione inviato dall'applicazione client, ad esempio chatbot|

## <a name="failover-regions"></a>Failover regions

Each region has a secondary region to fail over to. Europe fails over inside Europe and Australia fails over inside Australia.

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimento a entità predefinite](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
