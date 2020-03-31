---
title: 'Guida introduttiva: Riconoscimento vocale da un file audio - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037734"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere la voce da un file audio. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un file richiede solo pochi passaggi:
> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `AudioConfig` che specifica il nome del file WAV.
> * Creare un oggetto `SpeechRecognizer` tramite gli oggetti `SpeechConfig` e `AudioConfig` creati in precedenza.
> * Usando l'oggetto `SpeechRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `SpeechRecognitionResult` restituito.
