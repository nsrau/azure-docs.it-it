---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400309"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale in un file audio. Il servizio di sintesi vocale offre numerose opzioni per le voci sintetizzate, come indicato nella sezione sulle [lingue supportate per la sintesi vocale](../../../language-support.md#text-to-speech). Dopo aver soddisfatto alcuni prerequisiti, la sintesi vocale richiede solo cinque passaggi:
> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto Audio Configuratin che specifica il nome del file WAV.
> * Creare un oggetto `SpeechSynthesizer` tramite gli oggetti di configurazione creati in precedenza.
> * Usando l'oggetto `SpeechSynthesizer`, convertire il testo in sintesi vocale, salvando il risultato nel file audio specificato.
> * Verificare la presenza di errori nell'oggetto `SpeechSynthesizer` restituito.
