---
title: 'Guida introduttiva: Riconoscere la voce da un microfono - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961173"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere in modo interattivo i dati audio acquisiti da un microfono. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un microfono richiede solo quattro passaggi:

> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `SpeechRecognizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usando l'oggetto `SpeechRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `SpeechRecognitionResult` restituito.
