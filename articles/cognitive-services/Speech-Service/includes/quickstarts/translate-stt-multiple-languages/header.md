---
title: 'Guida introduttiva: Tradurre la voce in più lingue - Servizio Voce'
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
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980909"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per tradurre in modo interattivo la voce da una lingua a un'altra. Dopo aver soddisfatto alcuni prerequisiti, la traduzione della voce in testo richiede solo sei passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechTranslationConfig```` dalla chiave e dall'area di sottoscrizione.
> * Aggiornare l'oggetto ````SpeechTranslationConfig```` per specificare la lingua di origine del riconoscimento vocale.
> * Aggiornare l'oggetto ````SpeechTranslationConfig```` per specificare più lingue di destinazione per la traduzione.
> * Creare un oggetto ````TranslationRecognizer```` tramite l'oggetto ````SpeechTranslationConfig```` creato in precedenza.
> * Usando l'oggetto ````TranslationRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````TranslationRecognitionResult```` restituito.
