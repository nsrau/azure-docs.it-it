---
title: 'Guida introduttiva: Tradurre la voce in testo - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980692"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per tradurre in modo interattivo la voce di una lingua nel testo di un'altra. Dopo aver soddisfatto alcuni prerequisiti, la traduzione della voce in testo richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Aggiornare l'oggetto ````SpeechConfig```` per specificare le lingue di origine e di destinazione.
> * Creare un oggetto ````TranslationRecognizer```` tramite l'oggetto ````SpeechConfig```` creato in precedenza.
> * Usando l'oggetto ````TranslationRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````TranslationRecognitionResult```` restituito.
