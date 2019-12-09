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
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818010"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale in un file audio. Dopo aver soddisfatto alcuni prerequisiti, la sintesi vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto Audio Configuratin che specifica il nome del file WAV.
> * Creare un oggetto ````SpeechSynthesizer```` tramite gli oggetti di configurazione creati in precedenza.
> * Usando l'oggetto ````SpeechSynthesizer````, convertire il testo in sintesi vocale, salvando il risultato nel file audio specificato.
> * Verificare la presenza di errori nell'oggetto ````SpeechSynthesizer```` restituito.
