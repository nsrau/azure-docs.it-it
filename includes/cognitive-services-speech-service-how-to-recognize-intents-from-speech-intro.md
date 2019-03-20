---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "58114961"
---
<!-- N.B. no header, language-agnostic -->

[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) di Servizi cognitivi Microsoft offre un modo per riconoscere le **finalità dai contenuti vocali** ed è supportato dal [servizio Language Understanding Intelligent Service (LUIS)](https://www.luis.ai/home) di Servizi cognitivi.

1. Creare una configurazione di riconoscimento vocale con una chiave di sottoscrizione LUIS e un'[area](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) come parametri. La chiave di sottoscrizione LUIS viene chiamata **chiave di endpoint** nella documentazione di servizio. Non è possibile usare il servizio di creazione delle chiavi di LUIS (vedere la nota presente in seguito in questa sezione).

1. Creare un sistema di riconoscimento della finalità dalla configurazione per il riconoscimento vocale. Specificare una configurazione audio se si vuole effettuare il riconoscimento da un'origine diversa dal microfono predefinito (ad esempio, flusso audio o file audio).

1. Ottenere il modello di comprensione del linguaggio basato sull'**AppId**. Aggiungere la finalità desiderata.

1. Collegare gli eventi per il funzionamento asincrono, se desiderato. Il sistema di riconoscimento chiama quindi i gestori dell'evento quando hanno risultati intermedi e finali (incluse le finalità). Se gli eventi non sono collegati, l'applicazione riceve solo un risultato finale di trascrizione.

1. Avviare il riconoscimento delle finalità. Per un riconoscimento singolo, ad esempio riconoscimento di comando o una query, usare il metodo `RecognizeOnceAsync()`. Questo metodo restituisce la prima espressione riconosciuta. Per un riconoscimento a esecuzione prolungata usare il metodo `StartContinuousRecognitionAsync()`. Collegare gli eventi per risultati di riconoscimento asincroni.

Vedere i frammenti di codice seguenti per gli scenari delle finalità che usano Speech SDK. Sostituire i valori nell'esempio con la propria chiave di sottoscrizione LUIS (chiave di endpoint), l'[area della sottoscrizione](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) e l'**AppID** del modello di finalità.

> [!NOTE]
> A differenza di altri servizi supportati da Speech SDK, i servizi di riconoscimento finalità richiedono una chiave di sottoscrizione specifica (chiave di endpoint LUIS). Per informazioni sulla tecnologia di riconoscimento delle finalità, vedere il [sito Web di LUIS](https://www.luis.ai). Per informazioni su come acquisire la **chiave di endpoint**, vedere [Creare una chiave di endpoint di LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription).
