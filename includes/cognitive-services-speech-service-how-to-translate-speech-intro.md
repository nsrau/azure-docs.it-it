---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185115"
---
<!-- N.B. no header, language-agnostic -->

[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) di Servizi cognitivi Microsoft è il metodo più semplice per usare la **traduzione vocale** nell'applicazione.
L’SDK fornisce la funzionalità completa del servizio. Il processo di base per l'esecuzione della traduzione vocale include i passaggi seguenti:

1. Creare una configurazione della traduzione vocale e fornire come parametri una chiave di sottoscrizione del servizio di riconoscimento vocale (o un token di autorizzazione) e una [regione](~/articles/cognitive-services/speech-service/regions.md). Cambiare la configurazione in base alle esigenze. Ad esempio è possibile configurare le lingue di origine e di destinazione della traduzione, nonché specificare se si desidera un output vocale o di testo.

1. Creare un sistema di riconoscimento della traduzione nella configurazione della traduzione vocale. Specificare una configurazione audio se si desidera effettuare il riconoscimento da un'origine diversa dal microfono predefinito (ad esempio, flusso audio o file audio).

1. Collegare gli eventi per il funzionamento asincrono, se desiderato. Il sistema di riconoscimento chiama quindi i gestori dell'evento in presenza di risultati intermedi e finali, nonché un evento di sintesi per l'output audio facoltativo. In caso contrario, l'applicazione riceve solo un risultato finale di trascrizione.

1. Avviare il riconoscimento. Per una traduzione breve, usare il metodo `RecognizeOnceAsync()`, che restituisce la prima espressione riconosciuta. Per le traduzioni con esecuzione prolungata, usare il metodo `StartContinuousRecognitionAsync()` e collegare gli eventi per i risultati del riconoscimento asincrono.

Vedere i frammenti di codice seguenti per gli scenari di traduzione vocale che usano Speech SDK.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
