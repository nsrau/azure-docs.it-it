---
title: Aree - Servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Informazioni di riferimento sulle aree del servizio Voce.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: 32a8b4cbfd0d8b43d294155432a0feaf29036c4c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218139"
---
# <a name="speech-service-supported-regions"></a>Aree supportate dal servizio Voce

Il servizio Voce consente alle applicazioni di eseguire operazioni di riconoscimento e sintesi vocale, ovvero conversione di audio in testo e viceversa, e anche di traduzione vocale. Il servizio è disponibile in più aree con endpoint univoci per Speech SDK e le API REST.

Verificare di usare l'endpoint corrispondente all'area relativa alla propria sottoscrizione.

## <a name="speech-sdk"></a>Speech SDK

In [Speech Service SDK](speech-sdk.md) le aree vengono specificate sotto forma di stringa, ad esempio come parametro `SpeechConfig.FromSubscription` in Speech SDK per C#.

### <a name="speech-recognition-and-translation"></a>Riconoscimento vocale e traduzione vocale

Speech SDK è disponibile per il **riconoscimento vocale** e la **traduzione vocale** nelle aree seguenti:

  Region | Parametro Speech SDK | Portale di personalizzazione del servizio Voce
 ------|-------|--------
 Stati Uniti occidentali | `westus` | https://westus.cris.ai
 Stati Uniti occidentali 2 | `westus2` | https://westus2.cris.ai
 Stati Uniti orientali | `eastus` | https://eastus.cris.ai
 Stati Uniti Orientali 2 | `eastus2` | https://eastus2.cris.ai
 Asia orientale | `eastasia` | https://eastasia.cris.ai
 Asia sudorientale | `southeastasia` | https://southeastasia.cris.ai
 Europa settentrionale | `northeurope` | https://northeurope.cris.ai
 Europa occidentale | `westeurope` | https://westeurope.cris.ai


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

Per la documentazione di riferimento sul riconoscimento vocale, vedere [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Sintesi vocale

Per la documentazione di riferimento sulla sintesi vocale, vedere [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
