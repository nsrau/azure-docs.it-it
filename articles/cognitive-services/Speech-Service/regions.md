---
title: Aree-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Elenco delle aree e degli endpoint disponibili per il servizio riconoscimento vocale, tra cui sintesi vocale, sintesi vocale e traduzione vocale.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 478a62eaddcf3b9b831812a0930ff10c1adce99d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072450"
---
# <a name="speech-service-supported-regions"></a>Aree supportate dal servizio Voce

Il servizio Voce consente alle applicazioni di eseguire operazioni di riconoscimento e sintesi vocale, ovvero conversione di audio in testo e viceversa, e anche di traduzione vocale. Il servizio è disponibile in più aree con endpoint univoci per Speech SDK e le API REST.

Verificare di usare l'endpoint corrispondente all'area relativa alla propria sottoscrizione.

## <a name="speech-sdk"></a>Speech SDK

In [Speech SDK](speech-sdk.md) le aree vengono specificate sotto forma di stringa (ad esempio, come parametro `SpeechConfig.FromSubscription` in Speech SDK per C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Sintesi vocale, sintesi vocale, traduzione vocale

Speech SDK è disponibile in queste aree per il **riconoscimento vocale**, **la sintesi vocale**e la **traduzione**:

| Area           | Parametro Speech SDK | Portale di personalizzazione del servizio Voce    |
| ---------------- | -------------------- | ------------------------------ |
| Stati Uniti occidentali          | `westus`             | https://westus.cris.ai         |
| Stati Uniti occidentali 2        | `westus2`            | https://westus2.cris.ai        |
| Stati Uniti Orientali          | `eastus`             | https://eastus.cris.ai         |
| Stati Uniti orientali 2        | `eastus2`            | https://eastus2.cris.ai        |
| Stati Uniti centrali       | `centralus`          | https://centralus.cris.ai      |
| Stati Uniti centro-settentrionali | `northcentralus`     | https://northcentralus.cris.ai |
| Stati Uniti centro-meridionali | `southcentralus`     | https://southcentralus.cris.ai |
| India centrale    | `centralindia`       | https://centralindia.cris.ai   |
| Asia orientale        | `eastasia`           | https://eastasia.cris.ai       |
| Asia sudorientale   | `southeastasia`      | https://southeastasia.cris.ai  |
| Giappone orientale       | `japaneast`          | https://japaneast.cris.ai      |
| Corea del Sud centrale    | `koreacentral`       | https://koreacentral.cris.ai   |
| Australia orientale   | `australiaeast`      | https://australiaeast.cris.ai  |
| Canada centrale   | `canadacentral`      | https://canadacentral.cris.ai  |
| Europa settentrionale     | `northeurope`        | https://northeurope.cris.ai    |
| Europa occidentale      | `westeurope`         | https://westeurope.cris.ai     |
| Regno Unito meridionale         | `uksouth`            | https://uksouth.cris.ai        |
| Francia centrale   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Riconoscimento finalità

Le aree disponibili per il **riconoscimento finalità** tramite Speech SDK sono le seguenti:

| Regione globale | Area           | Parametro Speech SDK |
| ------------- | ---------------- | -------------------- |
| Asia          | Asia orientale        | `eastasia`           |
| Asia          | Asia sudorientale   | `southeastasia`      |
| Australia     | Australia orientale   | `australiaeast`      |
| Europa        | Europa settentrionale     | `northeurope`        |
| Europa        | Europa occidentale      | `westeurope`         |
| America del Nord | Stati Uniti Orientali          | `eastus`             |
| America del Nord | Stati Uniti orientali 2        | `eastus2`            |
| America del Nord | Stati Uniti centro-meridionali | `southcentralus`     |
| America del Nord | Stati Uniti centro-occidentali  | `westcentralus`      |
| America del Nord | Stati Uniti occidentali          | `westus`             |
| America del Nord | Stati Uniti occidentali 2        | `westus2`            |
| America del Sud | Brasile meridionale     | `brazilsouth`        |

Questo è un sottoinsieme delle aree di pubblicazione supportate per il [servizio Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Assistenti vocali

[Speech SDK](speech-sdk.md) supporta le funzionalità di **Assistente vocale** in queste aree:

| Area         | Parametro Speech SDK |
| -------------- | -------------------- |
| Stati Uniti occidentali        | `westus`             |
| Stati Uniti occidentali 2      | `westus2`            |
| Stati Uniti Orientali        | `eastus`             |
| Stati Uniti orientali 2      | `eastus2`            |
| Europa occidentale    | `westeurope`         |
| Europa settentrionale   | `northeurope`        |
| Asia sudorientale | `southeastasia`      |

## <a name="rest-apis"></a>API REST

Il servizio Voce espone anche endpoint REST per le richieste di riconoscimento vocale e sintesi vocale.

### <a name="speech-to-text"></a>Riconoscimento vocale

Per la documentazione di riferimento per la sintesi vocale, vedere [API REST di riconoscimento](rest-speech-to-text.md)vocale.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Sintesi vocale

Per la documentazione di riferimento da sintesi vocale, vedere [API REST di sintesi vocale](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
