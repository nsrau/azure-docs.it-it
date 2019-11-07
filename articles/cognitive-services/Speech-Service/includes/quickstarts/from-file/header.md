---
title: 'Guida introduttiva: Riconoscimento vocale da un file audio - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506284"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere la voce da un file audio. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````AudioConfig```` che specifica il nome del file WAV.
> * Creare un oggetto ````SpeechRecognizer```` tramite gli oggetti ````SpeechConfig```` e ````AudioConfig```` creati in precedenza.
> * Usando l'oggetto ````SpeechRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````SpeechRecognitionResult```` restituito.
