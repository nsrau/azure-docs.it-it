---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961581"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale. Il servizio di sintesi vocale offre numerose opzioni per le voci sintetizzate, come indicato nella sezione sulle [lingue supportate per la sintesi vocale](../../../language-support.md#text-to-speech). Dopo aver soddisfatto alcuni prerequisiti, il rendering della voce sintetizzata negli altoparlanti predefiniti richiede solo quattro passaggi:
> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `SpeechSynthesizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usare l'oggetto `SpeechSynthesizer` per pronunciare il testo.
> * Verificare la presenza di errori nell'oggetto `SpeechSynthesisResult` restituito.
