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
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379571"
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

Region| Valore per il parametro dell'area in Speech SDK| Portale
-|-
Stati Uniti occidentali| `westus`| https://westus.cris.ai
Stati Uniti occidentali 2| `westus2`| https://westus2.cris.ai
Stati Uniti orientali| `eastus`| https://eastus.cris.ai
Stati Uniti Orientali 2| `eastus2`| https://eastus2.cris.ai
Asia orientale| `eastasia`| https://eastasia.cris.ai
Asia sudorientale| `southeastasia`| https://southeastasia.cris.ai
Europa settentrionale| `northeurope`| https://northeurope.cris.ai
Europa occidentale|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Aree per il riconoscimento delle finalità

Le aree disponibili per **riconoscimento finalità** tramite Speech SDK sono elencate nella [pagina dell'area del servizio Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Per ogni area di pubblicazione elencata, il parametro dell'area Speech SDK corrispondente viene determinato come prima parte del nome di dominio dell'endpoint.
Ad esempio, usare `westus` per specificare l'area di pubblicazione Stati Uniti occidentali.
