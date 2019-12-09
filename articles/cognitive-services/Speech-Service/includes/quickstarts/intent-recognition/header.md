---
title: 'Guida introduttiva: Riconoscere la voce, le finalità e le entità - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816012"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere in modo interattivo i dati audio acquisiti da un microfono. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un microfono richiede solo quattro passaggi:
> [!div class="checklist"]
>
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````IntentRecognizer```` tramite l'oggetto ````SpeechConfig```` specificato.
> * Usando l'oggetto ````IntentRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````IntentRecognitionResult```` restituito.
