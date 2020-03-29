---
title: Assistenti vocali - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per gli assistenti vocali che utilizzano Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369897"
---
# <a name="what-is-a-voice-assistant"></a>Che cos'è un assistente vocale?

Gli assistenti vocali che utilizzano il servizio di riconoscimento vocale consentono agli sviluppatori di creare interfacce conversazionali naturali e simili a come l'uomo per le loro applicazioni ed esperienze.

Il servizio assistente vocale fornisce un'interazione rapida e affidabile tra un dispositivo e un'implementazione dell'assistente che utilizza (1) il canale Direct Line Speech di Bot Framework o (2) il servizio di comandi personalizzati (anteprima) integrato per il completamento delle attività.

Le applicazioni si connettono al servizio assistente vocale con Speech Software Development Kit (SDK).

   ![Diagramma concettuale del flusso del servizio di orchestrazione dell'assistente vocaleConceptual diagram of the voice assistant orchestration service flow](media/voice-assistants/overview.png "Il flusso dell'assistente vocale")

## <a name="choosing-an-assistant-solution"></a>Scelta di una soluzione assistente

Il primo passo per creare un assistente vocale è decidere cosa fare. Il servizio di riconoscimento vocale offre più soluzioni complementari per la creazione delle interazioni con l'assistente. Se si desidera la flessibilità e la versatilità che fornisce il canale [Direct Line Speech](direct-line-speech.md) di Bot Framework o la semplicità dei comandi personalizzati [(anteprima)](custom-commands.md) per scenari semplici, la selezione degli strumenti giusti consente di iniziare.

| Per... | Allora considera... | Per esempio... |
|-------------------|------------------|----------------|
|Conversazione aperta con robusta integrazione delle competenze e controllo completo della distribuzione | Canale [Direct Line Speech](direct-line-speech.md) di Bot Framework | <ul><li>"Devo andare a Seattle"</li><li>"Che tipo di pizza posso ordinare?"</li></ul>
|Comando e controllo o conversazione orientata alle attività con creazione e hosting semplificati | [Comandi personalizzati (anteprima)Custom Commands (Preview)](custom-commands.md) | <ul><li>"Accendere la luce dall'alto"</li><li>"Rendilo 5 gradi più caldo"</ul>

Se non sei ancora sicuro di cosa desideri che l'assistente gestisca, ti consigliamo [Direct Line Speech](direct-line-speech.md) come scelta predefinita. Offre l'integrazione con un ricco set di strumenti e strumenti di authoring come virtual [assistant Solution e Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) e il servizio [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) per basarsi su modelli comuni e utilizzare le fonti di conoscenza esistenti.

[I comandi personalizzati (anteprima)](custom-commands.md) offrono un'esperienza di creazione e hosting semplificata, specifica per gli scenari di controllo e comando del linguaggio naturale.

   ![Confronto delle soluzioni per gli assistenti](media/voice-assistants/assistant-solution-comparison.png "Confronto delle soluzioni per gli assistenti")

## <a name="core-features"></a>Caratteristiche principali

Indipendentemente dal fatto che tu scelga [Direct Line Speech](direct-line-speech.md) o Custom Commands [(Preview)](custom-commands.md) per creare le interazioni con l'assistente, puoi utilizzare un ricco set di funzionalità di personalizzazione per personalizzare l'assistente per il tuo marchio, prodotto e personalità.

| Category | Funzionalità |
|----------|----------|
|[Custom (parola chiave)](speech-devices-sdk-create-kws.md) | Gli utenti possono avviare conversazioni con gli assistenti con una parola chiave personalizzata come "Hey Contoso". Un'app esegue questa operazione con un motore di parole chiave personalizzato in Speech SDK, che può essere configurato con una parola chiave personalizzata [che è possibile generare qui.](speech-devices-sdk-create-kws.md) Gli assistenti vocali possono utilizzare la verifica delle parole chiave sul lato servizio per migliorare l'accuratezza dell'attivazione della parola chiave (rispetto al solo dispositivo).
|[Sintesi vocale per il testo](speech-to-text.md) | Gli assistenti vocali convertono l'audio in tempo reale in testo riconosciuto utilizzando la sintesi [vocale](speech-to-text.md) del servizio di riconoscimento vocale. Questo testo è disponibile, come viene trascritto, sia per l'implementazione dell'assistente che per l'applicazione client.
|[Sintesi vocale](text-to-speech.md) | Le risposte testuali dell'assistente vengono sintetizzate utilizzando la [sintesi vocale](text-to-speech.md) del servizio di riconoscimento vocale. Questa sintesi viene quindi resa disponibile per l'applicazione client come flusso audio. Microsoft offre la possibilità di creare la propria voce di risparmio multimediale neurale personalizzato e di alta qualità che dà una voce al proprio marchio. Per saperne di più, [contattaci](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Introduzione agli assistenti vocali

Offriamo guide introduttive progettate per avere l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive dell'assistente vocale, organizzate in base alla lingua.

| Guida introduttiva | Piattaforma | API (riferimento) |
|------------|----------|---------------|
| C,, UWP | WINDOWS | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Sfoglia](https://aka.ms/csspeech/javaref) |
| Java | Android | [Sfoglia](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente vocale è disponibile in GitHub.Sample code for creating a voice assistant is available on GitHub. In questi esempi viene trattata l'applicazione client per la connessione all'assistente in diversi linguaggi di programmazione comuni.

* [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)
* [Esercitazione: La voce abilita l'assistente con Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Esercitazione

Un'esercitazione su come [abilitare la voce all'assistente utilizzando l'SDK vocale e il canale Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati utilizzando il servizio di riconoscimento vocale possono utilizzare l'intera gamma di opzioni di personalizzazione disponibili per la [sintesi vocale,](speech-to-text.md) [la sintesi vocale](text-to-speech.md)e la selezione personalizzata delle [parole chiave.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua/impostazioni locali (vedere [Lingue supportate](supported-languages.md)).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio bot di AzureAzure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Ottenere Speech SDK](speech-sdk.md)
* [Ulteriori informazioni sui comandi personalizzati (anteprima)Learn more about Custom Commands (Preview)](custom-commands.md)
* [Ulteriori informazioni su Direct Line Speech](direct-line-speech.md)
