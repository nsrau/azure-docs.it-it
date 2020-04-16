---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400653"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per riconoscere interattivamente contenuti vocali dall'input del microfono e ottenere la trascrizione in testo dell'audio acquisito. È facile integrare questa funzionalità nelle app o nei dispositivi per le attività di riconoscimento comuni, ad esempio la trascrizione di conversazioni. Può essere usato anche per integrazioni più complesse, ad esempio l'uso di Bot Framework con Speech SDK per creare assistenti vocali.

Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale da un microfono richiede solo quattro passaggi:

> [!div class="checklist"]
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `SpeechRecognizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usando l'oggetto `SpeechRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `SpeechRecognitionResult` restituito.
