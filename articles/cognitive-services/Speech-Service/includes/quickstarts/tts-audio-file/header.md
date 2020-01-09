---
title: 'Avvio rapido: Eseguire la sintesi vocale in un file audio - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467282"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale in un file audio. Dopo aver soddisfatto alcuni prerequisiti, la sintesi vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto Audio Configuratin che specifica il nome del file WAV.
> * Creare un oggetto ````SpeechSynthesizer```` tramite gli oggetti di configurazione creati in precedenza.
> * Usando l'oggetto ````SpeechSynthesizer````, convertire il testo in sintesi vocale, salvando il risultato nel file audio specificato.
> * Verificare la presenza di errori nell'oggetto ````SpeechSynthesizer```` restituito.
