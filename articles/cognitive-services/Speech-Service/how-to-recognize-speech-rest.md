---
title: Riconoscimento vocale con l'API REST
description: Informazioni su come usare il Riconoscimento vocale nel servizio Voce
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331280"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Riconoscimento vocale con l'API REST

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

L'API REST può essere usata per riconoscere brevi espressioni tramite una richiesta HTTP POST.

L'API REST è il metodo più semplice di riconoscimento vocale se non si utilizza una lingua supportata dall’SDK.
Si effettua una richiesta HTTP POST all'endpoint del servizio, passando l'intera espressione nel corpo della richiesta.
Si riceve una risposta che contiene il testo riconosciuto.

> [!NOTE]
> Le espressioni sono limitate a 15 secondi o meno quando si utilizza l'API REST.
> Vedere [Speech SDK](how-to-recognize-speech-csharp.md) per il riconoscimento di espressioni più lunghe.

Per altre informazioni sull'API REST di **riconoscimento vocale**, vedere [API REST](rest-apis.md#speech-to-text). Per visualizzarlo in azione, scaricare gli [esempi di API REST](https://github.com/Azure-Samples/SpeechToText-REST) da GitHub.

## <a name="next-steps"></a>Passaggi successivi

- [Vedere la panoramica dell'API REST](rest-apis.md)
