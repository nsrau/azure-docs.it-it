---
title: 'Avvio rapido: Riconoscimento vocale da un file audio - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468027"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere la voce da un file audio. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````AudioConfig```` che specifica il nome del file WAV.
> * Creare un oggetto ````SpeechRecognizer```` tramite gli oggetti ````SpeechConfig```` e ````AudioConfig```` creati in precedenza.
> * Usando l'oggetto ````SpeechRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````SpeechRecognitionResult```` restituito.
