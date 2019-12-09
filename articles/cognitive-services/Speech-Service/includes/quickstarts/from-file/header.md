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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819419"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere la voce da un file audio. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````AudioConfig```` che specifica il nome del file WAV.
> * Creare un oggetto ````SpeechRecognizer```` tramite gli oggetti ````SpeechConfig```` e ````AudioConfig```` creati in precedenza.
> * Usando l'oggetto ````SpeechRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````SpeechRecognitionResult```` restituito.
