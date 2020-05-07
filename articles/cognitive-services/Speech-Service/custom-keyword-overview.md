---
title: Parole chiave personalizzate-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Cenni preliminari sulle funzionalità, le funzionalità e le restrizioni per le parole chiave personalizzate che usano Speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802181"
---
# <a name="what-is-a-keyword"></a>Che cos'è una parola chiave?

Una parola chiave è una parola o una frase breve che consente di attivare la voce del prodotto. Ad esempio, "Hey Cortana" è la parola chiave per Cortana Assistant. L'attivazione vocale consente agli utenti di iniziare a interagire con il prodotto senza alcuna pratica, semplicemente pronunciando la parola chiave. Poiché il prodotto è costantemente in ascolto per la parola chiave, tutto l'audio viene elaborato localmente sul dispositivo dell'utente fino a quando non si verifica un rilevamento per assicurarsi che i dati rimangano il più possibile privati.

## <a name="core-features-of-custom-keyword"></a>Funzionalità principali della parola chiave Custom

Con le funzionalità di personalizzazione, prestazioni e integrazione delle parole chiave personalizzate, è possibile personalizzare l'attivazione vocale per soddisfare al meglio la visione del prodotto e le esigenze degli utenti.

| Funzionalità | Descrizione |
|----------|----------|
| Personalizzazione delle parole chiave | Come estensione del marchio, una parola chiave rinforza l'equità creata con i clienti. Il portale di parole chiave personalizzato in speech studio consente di specificare qualsiasi parola o breve frase che meglio rappresenta il marchio. È possibile personalizzare ulteriormente la parola chiave scegliendo le pronunce corrette, che verranno rispettate dal modello di parola chiave generato.
| Verifica parole chiave | Quando la parola chiave viene rilevata localmente in modo sicuro, l'audio viene inviato al cloud per un'ulteriore verifica che un utente abbia detto la parola chiave. La verifica delle parole chiave offre un ulteriore livello di sicurezza, riducendo l'effetto di un rilevamento locale errato e la protezione della privacy degli utenti.
| Assistente vocale & l'integrazione dell'SDK vocale | Le parole chiave generate dalla parola chiave Custom in speech studio possono essere integrate facilmente all'interno del dispositivo o dell'applicazione tramite l'SDK di riconoscimento vocale. È sufficiente puntare l'SDK al modello di parola chiave fornito da speech studio e il prodotto sarà attivato tramite Voice, supportato dalla verifica delle parole chiave. Per completare l'esperienza vocale del prodotto, è possibile creare un proprio [Assistente vocale](voice-assistants.md).

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per l'integrazione di un modello di parola chiave è disponibile su GitHub. Questi esempi coprono l'applicazione client per l'integrazione della parola chiave in diversi linguaggi di programmazione più diffusi.

* [Esercitazione: attivare la voce del prodotto con l'SDK di riconoscimento vocale, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)

## <a name="tutorial"></a>Esercitazione

* Come [creare una parola chiave personalizzata usando speech studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* Come [attivare l'attivazione vocale del prodotto tramite Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Linee guida per la denominazione delle parole chiave personalizzate](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Creare una parola chiave personalizzata](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [Ottenere Speech SDK](speech-sdk.md)
* [Altre informazioni sugli assistenti vocali](voice-assistants.md)
