---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144397"
---
<!-- N.B. no header, language-agnostic -->

[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) di Servizi cognitivi Microsoft è il metodo più semplice per usare la **traduzione vocale** nell'applicazione.
L’SDK fornisce la funzionalità completa del servizio. Il processo di base per l'esecuzione della traduzione vocale include i passaggi seguenti:

1. Creare una classe factory di riconoscimento vocale e specificare una chiave di sottoscrizione o un token di autorizzazione del servizio di riconoscimento vocale e un'[area](~/articles/cognitive-services/speech-service/regions.md) come parametri.
   
1. Creare un sistema di riconoscimento di traduzione dalla classe factory di riconoscimento vocale. È possibile configurare le lingue di traduzione di origine e di destinazione, nonché specificare se si desidera un output vocale o di testo. Esistono diverse varianti di sistemi di riconoscimento in base alla sorgente audio usata.

1. Collegare gli eventi per il funzionamento asincrono, se desiderato. Il sistema di riconoscimento chiama quindi i gestori dell'evento in presenza di risultati intermedi e finali, nonché un evento di sintesi per l'output audio facoltativo. In caso contrario, l'applicazione riceve solo un risultato finale di trascrizione.

1. Avviare il riconoscimento. Per una traduzione breve, usare il metodo `RecognizeAsync()`, che restituisce la prima espressione riconosciuta. Per le traduzioni con esecuzione prolungata, usare il metodo `StartContinuousRecognitionAsync()` e collegare gli eventi per i risultati del riconoscimento asincrono.

Vedere i frammenti di codice seguenti per gli scenari di traduzione vocale che usano Speech SDK.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
