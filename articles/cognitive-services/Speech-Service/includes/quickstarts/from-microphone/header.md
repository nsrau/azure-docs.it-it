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
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818816"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere in modo interattivo contenuti vocali dall'input del microfono e ottenere la trascrizione del testo dall'audio acquisito. È facile integrare questa funzionalità nelle app o nei dispositivi per le attività di riconoscimento comuni, ad esempio la trascrizione di conversazioni. Può essere usato anche per integrazioni più complesse, ad esempio l'uso di Bot Framework con Speech SDK per creare assistenti vocali.

Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un microfono richiede solo quattro passaggi:

> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `SpeechRecognizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usando l'oggetto `SpeechRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `SpeechRecognitionResult` restituito.
