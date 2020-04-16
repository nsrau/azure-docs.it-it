---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400418"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale. Il servizio di sintesi vocale offre numerose opzioni per le voci sintetizzate, come indicato nella sezione sulle [lingue supportate per la sintesi vocale](../../../language-support.md#text-to-speech). Dopo aver soddisfatto alcuni prerequisiti, il rendering della voce sintetizzata negli altoparlanti predefiniti richiede solo quattro passaggi:
> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `SpeechSynthesizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usare l'oggetto `SpeechSynthesizer` per pronunciare il testo.
> * Verificare la presenza di errori nell'oggetto `SpeechSynthesisResult` restituito.
