---
title: Aree del servizio Voce
titlesuffix: Azure Cognitive Services
description: Informazioni di riferimento sulle aree del servizio Voce.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 088e581da7511797a0f39959d867c6298262462a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242331"
---
# <a name="regions-of-the-speech-service"></a>Aree del servizio Voce

Il servizio Voce è disponibile in diverse aree.
Quando si crea una sottoscrizione è possibile selezionare un'area disponibile in base alle esigenze.

Quando si usa la sottoscrizione è necessario considerare l'area selezionata.

## <a name="rest-api"></a>API REST

Usare l'API REST per selezionare gli endpoint corretti specifici dell'area.
Vedere [API REST](rest-apis.md) per informazioni dettagliate.

## <a name="speech-sdk"></a>Speech SDK

In [Speech Service SDK](speech-sdk.md) le aree vengono specificate sotto forma di stringa, ad esempio come parametro `SpeechConfig.FromSubscription` in Speech SDK per C#.

### <a name="regions-for-speech-recognition-and-translation"></a>Aree per il riconoscimento vocale e la traduzione vocale

Nella tabella seguente sono elencate le aree disponibili per **riconoscimento vocale** e **traduzione**.

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


### <a name="regions-for-intent-recognition"></a>Aree per il riconoscimento delle finalità

Le aree disponibili per **riconoscimento finalità** tramite Speech SDK sono elencate nella [pagina dell'area del servizio Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Per ogni area di pubblicazione elencata, il parametro dell'area Speech SDK corrispondente viene determinato come prima parte del nome di dominio dell'endpoint.
Ad esempio, usare `westus` per specificare l'area di pubblicazione Stati Uniti occidentali.
