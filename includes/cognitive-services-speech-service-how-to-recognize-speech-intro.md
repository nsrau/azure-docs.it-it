---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 3394fc2f6395799a252b645635d982b4573296c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47000722"
---
<!-- N.B. no header, no intents here, language-agnostic -->

[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) di Servizi cognitivi fornisce il modo più semplice di usare il **Riconoscimento vocale** nell'applicazione con tutte le funzionalità.

1. Creare una configurazione per il riconoscimento vocale, fornire una chiave di sottoscrizione (o un token di autorizzazione) per il servizio di riconoscimento vocale e un'[area](~/articles/cognitive-services/speech-service/regions.md) come parametri. Cambiare la configurazione in base alle esigenze. Ad esempio, fornire un endpoint personalizzato per specificare un endpoint del servizio non standard oppure selezionare la lingua di input vocale o il formato di output.

1. Creare un sistema di riconoscimento vocale dalla configurazione per il riconoscimento vocale. Specificare una configurazione audio se si desidera effettuare il riconoscimento da un'origine diversa dal microfono predefinito (ad esempio, flusso audio o file audio).

1. Collegare gli eventi per il funzionamento asincrono, se desiderato. Il sistema di riconoscimento chiama quindi i gestori dell'evento quando hanno risultati intermedi e finali. In caso contrario, l'applicazione riceve solo un risultato finale di trascrizione.

1. Avviare il riconoscimento. Per un riconoscimento singolo, ad esempio riconoscimento di comando o una query, usare il metodo `RecognizeOnceAsync()`. Questo metodo restituisce la prima espressione riconosciuta. Per un riconoscimento a esecuzione prolungata, ad esempio per la trascrizione, usare il metodo `StartContinuousRecognitionAsync()`. Collegare gli eventi per risultati di riconoscimento asincroni.

Vedere i frammenti di codice seguenti per gli scenari di riconoscimento vocale che usano Speech SDK.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
