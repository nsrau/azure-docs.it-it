---
title: Aree del servizio Voce
description: Riferimento per le aree del servizio Speech.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 082002b25b02e1e496221f4686d0e636630dd438
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324392"
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

### <a name="regions-for-speech-recognition-and-translation"></a>Aree per il riconoscimento vocale e la traduzione vocale

Nella tabella seguente sono elencate le aree disponibili per **riconoscimento vocale** e **traduzione**:

Region| Valore per il parametro dell'area in Speech SDK
-|-
Stati Uniti occidentali| `westus`
Asia orientale| `eastasia`
Europa settentrionale| `northeurope`

### <a name="regions-for-intent-recognition"></a>Aree per il riconoscimento delle finalità

Le aree disponibili per **riconoscimento finalità** tramite Speech SDK sono elencate nella [pagina dell'area del servizio Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Per ogni area di pubblicazione elencata, il parametro dell'area Speech SDK corrispondente viene determinato come prima parte del nome di dominio dell'endpoint.
Ad esempio, usare `westus` per specificare l'area di pubblicazione Stati Uniti occidentali.
