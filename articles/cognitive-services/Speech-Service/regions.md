---
title: Aree - Servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Informazioni di riferimento sulle aree del servizio Voce.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 518f1048224a97943756c5b51b83cd509f82e11e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072505"
---
# <a name="speech-service-supported-regions"></a>Aree supportate dal servizio Voce

Il servizio Voce consente alle applicazioni di eseguire operazioni di riconoscimento e sintesi vocale, ovvero conversione di audio in testo e viceversa, e anche di traduzione vocale. Il servizio è disponibile in più aree con endpoint univoci per Speech SDK e le API REST.

Verificare di usare l'endpoint corrispondente all'area relativa alla propria sottoscrizione.

## <a name="speech-sdk"></a>Speech SDK

In [Speech SDK](speech-sdk.md) le aree vengono specificate sotto forma di stringa (ad esempio, come parametro `SpeechConfig.FromSubscription` in Speech SDK per C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Per il riconoscimento vocale, sintesi vocale e traduzione

Speech SDK è disponibile nelle aree seguenti per **riconoscimento vocale**, **sintesi vocale**, e **traduzione**:

  Region | Parametro Speech SDK | Portale di personalizzazione del servizio Voce
 ------|-------|--------
 Stati Uniti occidentali | `westus` | https://westus.cris.ai
 Stati Uniti occidentali 2 | `westus2` | https://westus2.cris.ai
 Stati Uniti orientali | `eastus` | https://eastus.cris.ai
 Stati Uniti Orientali 2 | `eastus2` | https://eastus2.cris.ai
 Stati Uniti centrali | `centralus` | https://centralus.cris.ai
 Stati Uniti centro-settentrionali | `northcentralus` | https://northcentralus.cris.ai
 Stati Uniti centro-meridionali | `southcentralus` | https://southcentralus.cris.ai
 India centrale | `centralindia` | https://centralindia.cris.ai
 Asia orientale | `eastasia` | https://eastasia.cris.ai
 Asia sudorientale | `southeastasia` | https://southeastasia.cris.ai
 Giappone orientale | `japaneast` | https://japaneast.cris.ai
 Corea del Sud centrale | `koreacentral` | https://koreacentral.cris.ai
 Australia orientale | `australiaeast` | https://australiaeast.cris.ai
 Canada centrale | `canadacentral` | https://canadacentral.cris.ai
 Europa settentrionale | `northeurope` | https://northeurope.cris.ai
 Europa occidentale | `westeurope` | https://westeurope.cris.ai
 Regno Unito meridionale | `uksouth` | https://uksouth.cris.ai
 Francia centrale | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Riconoscimento finalità

Le aree disponibili per il **riconoscimento finalità** tramite Speech SDK sono le seguenti:

 Regione globale | Region | Parametro Speech SDK
 ------|-------|--------
 Asia | Asia orientale | `eastasia`
 Asia | Asia sud-orientale | `southeastasia`
 Australia | Australia orientale | `australiaeast`
 Europa | Europa settentrionale | `northeurope`
 Europa | Europa occidentale | `westeurope`
 America del Nord | Stati Uniti orientali | `eastus`
 America del Nord | Stati Uniti orientali 2 | `eastus2`
 America del Nord | Stati Uniti centro-meridionali | `southcentralus`
 America del Nord | Stati Uniti centro-occidentali | `westcentralus`
 America del Nord | Stati Uniti occidentali | `westus`
 America del Nord | Stati Uniti occidentali 2 | `westus2`
 America del Sud | Brasile meridionale | `brazilsouth`

Questo è un sottoinsieme delle aree di pubblicazione supportate per il [servizio Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>API REST

Il servizio Voce espone anche endpoint REST per le richieste di riconoscimento vocale e sintesi vocale.

### <a name="speech-to-text"></a>Riconoscimento vocale

Per documentazione di riferimento per il riconoscimento vocale, vedere [API REST per il riconoscimento vocale](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Sintesi vocale

Per documentazione di riferimento di sintesi vocale, vedere [API REST di sintesi vocale](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
