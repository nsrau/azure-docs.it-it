---
title: 'Guida introduttiva: Riconoscimento vocale da un file audio - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400616"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere la voce da un file audio. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un file richiede solo pochi passaggi:
> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `AudioConfig` che specifica il nome del file WAV.
> * Creare un oggetto `SpeechRecognizer` tramite gli oggetti `SpeechConfig` e `AudioConfig` creati in precedenza.
> * Usando l'oggetto `SpeechRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `SpeechRecognitionResult` restituito.
