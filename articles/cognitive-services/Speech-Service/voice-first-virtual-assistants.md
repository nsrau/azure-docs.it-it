---
title: Personalizzato voice-first virtuali assistenti (anteprima) - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica della funzionalità, le funzionalità e restrizioni per assistenti virtuali personalizzati voice-first usando il canale Direct Line vocale per Bot Framework e il Cognitive Services vocale Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: c9ed6f898f4bcf8a5d4b2548cc1f3cf51f71bb2a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138172"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Sull'anteprima personalizzato assistenti virtuali voice-first

Assistenti virtuali personalizzati usando servizi di riconoscimento vocale di Azure permettono agli sviluppatori di creare interfacce discorsive naturale, simile a umane per le applicazioni ed esperienze. Canale Direct Line vocale di Bot Framework migliora tali funzionalità, fornendo un punto di ingresso coordinato, orchestrato da un robot compatibile che consente la voce in, voice interazione con bassa latenza e affidabilità elevata. I bot è possibile usare Microsoft LUIS (Language Understanding) per l'interazione del linguaggio naturale. Direct Line vocale è accessibile dai dispositivi usando il Software Development Kit (SDK) di riconoscimento vocale.

   ![Diagramma concettuale del flusso di servizio di orchestrazione di riconoscimento vocale linea diretta](media/voice-first-virtual-assistants/overview.png "flusso il canale di riconoscimento vocale")

Direct Line vocale e le relative funzionalità per personalizzato assistenti virtuali voice-first sono un supplemento ideale per la [virtuale soluzione Assistant](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction) e [modello Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Anche se Direct Line vocale possa funzionare con qualsiasi bot compatibile, queste risorse forniscono una linea di base riutilizzabile per i modelli per iniziare rapidamente, nonché competenze di supporta comuni ed esperienze colloquiale di alta qualità.

## <a name="core-features"></a>Funzionalità di base

| Category | Funzionalità |
|----------|----------|
|[Word riattivazione personalizzato](speech-devices-sdk-create-kws.md) | È possibile abilitare gli utenti avviare le conversazioni con i robot usando una parola chiave personalizzata, ad esempio "Ehi Contoso". Questa attività viene eseguita con un motore di word personalizzato riattivazione nel Speech SDK, che può essere configurato con una parola personalizzato riattivazione [che è possibile generare qui](speech-devices-sdk-create-kws.md). Il canale Direct Line vocale include la verifica del word riattivazione lato servizio che consente di migliorare l'accuratezza dell'attivazione riattivazione word e il dispositivo da solo.
|[Riconoscimento vocale](speech-to-text.md) | Il canale Direct Line vocale include la trascrizione in tempo reale di audio in testo riconosciuto usando [vocale-](speech-to-text.md) vocale da servizi di Azure. Questo testo è disponibile per il bot e l'applicazione client come lo è trascritto.
|[Sintesi vocale](text-to-speech.md) | Risposte testuale dal tuo bot verranno essere sintetizzate usando [sintesi vocale](text-to-speech.md) vocale da servizi di Azure. Questo sintesi verranno reso disponibile affinché l'applicazione client come un flusso audio. Microsoft offre la possibilità di creare il proprio personalizzato e di alta qualità neurale sintesi vocale che offra una voce per il tuo marchio, per altre informazioni [Contattaci](mailto:mstts@microsoft.com).
|[Riconoscimento vocale Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Come un canale all'interno di Bot Framework, Direct Line vocale consente la connessione diretto e immediata tra l'applicazione client, un bot compatibile e le funzionalità di riconoscimento vocale di servizi di Azure. Per altre informazioni sulla configurazione del tuo bot per l'utilizzo del canale Direct Line vocale, vedere [relativa pagina nella documentazione di Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Codice di esempio

Codice di esempio per la creazione di un Assistente virtuale voice-first è disponibile in GitHub. Questi esempi coprono l'applicazione client per la connessione al tuo bot in diversi linguaggi di programmazione più diffusi.

* [Esempi di assistenti virtuali Voice-first (SDK)](https://aka.ms/csspeech/samples)
* [Guida introduttiva: voice-first assistenti virtuali (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Guida introduttiva: voice-first virtuali assistenti (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Personalizzazione

Voice-first assistenti virtuali creati con servizi di riconoscimento vocale di Azure è possono usare l'intera gamma di opzioni di personalizzazione disponibili per [per il riconoscimento vocale](speech-to-text.md), [sintesi vocale](text-to-speech.md), e [parola chiave personalizzata selezione](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Le opzioni di personalizzazione variano a seconda lingua/impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
* [Ottenere il SDK di riconoscimento vocale](speech-sdk.md)
* [Creare e distribuire un bot di base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Ottenere la soluzione Assistant virtuale e il modello dell'organizzazione](https://github.com/Microsoft/AI)
