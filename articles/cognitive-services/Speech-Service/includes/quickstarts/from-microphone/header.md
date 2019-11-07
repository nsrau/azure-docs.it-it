---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono - Servizio Voce'
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
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505564"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere in modo interattivo i dati audio acquisiti da un microfono. Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un microfono richiede solo quattro passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````SpeechRecognizer```` tramite l'oggetto ````SpeechConfig```` specificato.
> * Usando l'oggetto ````SpeechRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````SpeechRecognitionResult```` restituito.
