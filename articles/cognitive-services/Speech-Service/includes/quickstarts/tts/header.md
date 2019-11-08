---
title: 'Guida introduttiva: Sintesi vocale - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale con Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505076"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale. Dopo aver soddisfatto alcuni prerequisiti, il rendering della voce sintetizzata negli altoparlanti predefiniti richiede solo quattro passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````SpeechSynthesizer```` tramite l'oggetto ````SpeechConfig```` specificato.
> * Usare l'oggetto ````SpeechSynthesizer```` per pronunciare il testo.
> * Verificare la presenza di errori nell'oggetto ````SpeechSynthesisResult```` restituito.
