---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961439"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale in un file audio. Il servizio di sintesi vocale offre numerose opzioni per le voci sintetizzate, come indicato nella sezione sulle [lingue supportate per la sintesi vocale](../../../language-support.md#text-to-speech). Dopo aver soddisfatto alcuni prerequisiti, la sintesi vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto Audio Configuratin che specifica il nome del file WAV.
> * Creare un oggetto `SpeechSynthesizer` tramite gli oggetti di configurazione creati in precedenza.
> * Usando l'oggetto `SpeechSynthesizer`, convertire il testo in sintesi vocale, salvando il risultato nel file audio specificato.
> * Verificare la presenza di errori nell'oggetto `SpeechSynthesizer` restituito.
