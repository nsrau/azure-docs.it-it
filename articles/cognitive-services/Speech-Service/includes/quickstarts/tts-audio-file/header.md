---
title: 'Guida introduttiva: Eseguire la sintesi vocale in un file audio - Servizio Voce'
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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504924"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale in un file audio. Dopo aver soddisfatto alcuni prerequisiti, la sintesi vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto Audio Configuratin che specifica il nome del file WAV.
> * Creare un oggetto ````SpeechSynthesizer```` tramite gli oggetti di configurazione creati in precedenza.
> * Usando l'oggetto ````SpeechSynthesizer````, convertire il testo in sintesi vocale, salvando il risultato nel file audio specificato.
> * Verificare la presenza di errori nell'oggetto ````SpeechSynthesizer```` restituito.
