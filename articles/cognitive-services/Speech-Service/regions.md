---
title: Aree del servizio Speech | Microsoft Docs
description: Riferimento per le aree del servizio Speech.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 11360d163fdba057d373d091d46903cde7789a8b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071420"
---
# <a name="regions-of-the-speech-service"></a>Aree del servizio Speech

Il servizio Speech è disponibile in aree diverse.
Quando si crea una sottoscrizione è possibile selezionare un'area disponibile, in base alle esigenze.

Quando si usa la sottoscrizione è necessario considerare l'area selezionata.

## <a name="rest-api"></a>API REST

Usando l'API REST, selezionare gli endpoint specifici dell'area a destra.
Vedere [API REST](rest-apis.md) per informazioni dettagliate.

## <a name="speech-sdk"></a>Speech SDK

In [Speech SDK](speech-sdk.md) le aree vengono specificate sotto forma di stringa (ad esempio, come parametro [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) in Speech SDK per C#).

Nella tabella seguente sono elencate le aree disponibili per **riconoscimento vocale** e **traduzione**:

Region| Valore per il parametro dell'area in Speech SDK
-|-
Stati Uniti occidentali| `westus`
Asia orientale| `eastasia`
Europa settentrionale| `northeurope`

Le aree disponibili per **riconoscimento finalità** tramite Speech SDK sono elencate nella [pagina dell'area del servizio Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Per ogni area di pubblicazione elencata, il parametro dell'area Speech SDK corrispondente viene determinato come prima parte del nome di dominio dell'endpoint.
Ad esempio, usare `westus` per specificare l'area di pubblicazione Stati Uniti occidentali.
