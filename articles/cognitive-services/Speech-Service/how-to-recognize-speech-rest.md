---
title: Riconoscimento vocale con l'API REST
titleSuffix: Azure Cognitive Services
description: Informazioni sull'uso dell'API di riconoscimento vocale nel servizio Voce di Servizi cognitivi di Azure.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: 7c7bbaa986a6efdb82a50048c7c218f96cd4014a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220332"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Riconoscimento vocale con l'API REST

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

L'API REST può essere usata per riconoscere brevi espressioni del parlato tramite una richiesta HTTP POST.

L'API REST offre il metodo di riconoscimento vocale più semplice se non si usa una lingua supportata dall'[SDK](speech-sdk.md). Si effettua una richiesta HTTP POST all'endpoint del servizio e si passa l'intera espressione nel corpo della richiesta. Si riceve quindi una risposta contenente il testo riconosciuto.

> [!NOTE]
> Quando si usa l'API REST, la durata delle espressioni non può superare i 15 secondi.
> Vedere [Speech SDK](how-to-recognize-speech-csharp.md) per il riconoscimento di espressioni più lunghe.

Per altre informazioni sull'API REST di **riconoscimento vocale**, vedere l'articolo [API REST](rest-apis.md#speech-to-text-api). Per vedere come funziona in pratica l'API, scaricare gli [esempi di API REST](https://github.com/Azure-Samples/SpeechToText-REST) da GitHub.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [panoramica delle API REST](rest-apis.md).
