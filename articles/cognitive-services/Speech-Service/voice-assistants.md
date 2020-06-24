---
title: Assistenti vocali-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per gli assistenti vocali che usano il Software Development Kit (SDK) di riconoscimento vocale.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 18a9de8a2eaa2364e89e831db8dab5cbbb061c10
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299246"
---
# <a name="what-is-a-voice-assistant"></a>Che cos'è un assistente vocale?

Gli assistenti vocali che usano il servizio riconoscimento vocale consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per le proprie applicazioni ed esperienze.

Il servizio Voice Assistant fornisce un'interazione rapida e affidabile tra un dispositivo e un'implementazione di assistente che usa (1) la voce [Direct Line](direct-line-speech.md) (tramite il servizio Azure bot) per aggiungere funzionalità vocali ai bot, oppure, (2) comandi personalizzati per gli scenari di comando vocale.

## <a name="choosing-an-assistant-solution"></a>Scelta di una soluzione di assistente

Il primo passaggio per la creazione di un assistente vocale è quello di decidere cosa fare. Il servizio di riconoscimento vocale offre più soluzioni complementari per la creazione di interazioni con gli assistenti. È possibile aggiungere funzionalità Voice in e Voice out ai bot flessibili e versatili creati usando il servizio Azure bot con il canale di [sintesi vocale Direct Line](direct-line-speech.md) o sfruttare la semplicità di creazione di un'app di [comandi personalizzata](custom-commands.md) per gli scenari di comandi vocali semplici.

| Per... | Quindi, prendere in considerazione... | Per esempio... |
|-------------------|------------------|----------------|
|Conversazione aperta con Solid Skills Integration e controllo completo della distribuzione | Bot del servizio bot di Azure con canale [vocale Direct Line](direct-line-speech.md) | <ul><li>"Devo andare a Seattle"</li><li>"Che tipo di pizza è possibile ordinare?"</li></ul>
|Comandi vocali o conversazioni semplici orientate alle attività con creazione e hosting semplificate | [Comandi personalizzati](custom-commands.md) | <ul><li>"Accendere la luce del sovraccarico"</li><li>"Renderlo 5 gradi più caldo"</li><li>Altri esempi [disponibili qui](https://speech.microsoft.com/customcommands)</li></ul>

Se non si è certi di cosa si vuole che l'assistente debba gestire, è consigliabile usare [Direct Line Speech](direct-line-speech.md) come la scelta migliore. Offre l'integrazione con un set completo di strumenti e strumenti per la creazione, ad esempio la [soluzione Virtual Assistant e il modello Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , e il [servizio QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) per creare modelli comuni e usare le origini delle informazioni esistenti.

I [comandi personalizzati](custom-commands.md) semplificano la creazione di app avanzate per i comandi vocali ottimizzate per esperienze di interazione vocali-First. Offre un'esperienza di creazione unificata, un modello di hosting automatico e una complessità relativamente inferiore, che ti aiuta a concentrarti sulla creazione della soluzione migliore per gli scenari di comandi vocali.

   ![Confronto tra soluzioni di assistente](media/voice-assistants/assistant-solution-comparison.png "Confronto tra soluzioni di assistente")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Architettura di riferimento per la creazione di un assistente vocale con l'SDK vocale

   ![Diagramma concettuale del flusso del servizio di orchestrazione di Assistente vocale](media/voice-assistants/overview.png "Il flusso di Assistente vocale")

## <a name="core-features"></a>Funzionalità di base

Indipendentemente dal fatto che [si scelgano](direct-line-speech.md) i [comandi personalizzati](custom-commands.md) per la creazione di interazioni con gli assistenti, è possibile usare un set completo di funzionalità di personalizzazione per personalizzare l'assistente per il proprio marchio, il prodotto e la personalità.

| Category | Funzionalità |
|----------|----------|
|[Parola chiave Custom](speech-devices-sdk-create-kws.md) | Gli utenti possono avviare conversazioni con gli assistenti con una parola chiave personalizzata, ad esempio "Hey contoso". Un'app esegue questa operazione con un motore di parole chiave personalizzato nell'SDK di riconoscimento vocale, che può essere configurato con una parola chiave personalizzata [che è possibile generare qui](speech-devices-sdk-create-kws.md). Gli assistenti vocali possono usare la verifica delle parole chiave sul lato servizio per migliorare l'accuratezza dell'attivazione della parola chiave (rispetto al dispositivo da solo).
|[Riconoscimento vocale](speech-to-text.md) | Gli assistenti vocali convertono l'audio in tempo reale in testo riconosciuto usando il [riconoscimento vocale](speech-to-text.md) dal servizio di riconoscimento vocale. Questo testo è disponibile, in quanto viene trascritto, sia per l'implementazione dell'assistente che per l'applicazione client.
|[Sintesi vocale](text-to-speech.md) | Le risposte testuali dell'Assistente vengono sintetizzate tramite sintesi [vocale](text-to-speech.md) dal servizio di riconoscimento vocale. Questa sintesi viene quindi resa disponibile all'applicazione client come flusso audio. Microsoft offre la possibilità di creare una voce personalizzata neurale e di alta qualità che fornisce una voce al proprio marchio. Per altre informazioni, [Contattaci](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Introduzione agli assistenti vocali

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive per Assistente vocale, organizzate in base alla lingua.

* [Guida introduttiva: creare un assistente vocale personalizzato usando Direct Line Speech](quickstarts/voice-assistants.md)
* [Guida introduttiva: creare un'app di comando vocale con comandi personalizzati](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Esempio di codice ed esercitazioni

Il codice di esempio per la creazione di un assistente vocale è disponibile su GitHub. Questi esempi coprono l'applicazione client per la connessione all'assistente in diversi linguaggi di programmazione più diffusi.

* [Esempi di Assistente vocale su GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Esercitazione: abilitare l'assistente creato con il servizio Azure bot con l'SDK per sintesi vocale di C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Esercitazione: creare un'applicazione di comandi personalizzati con comandi vocali semplici](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati con servizi di riconoscimento vocale di Azure possono usare l'intera gamma di opzioni di personalizzazione.

* [Riconoscimento vocale personalizzato](how-to-custom-speech.md)
* [Voce personalizzata](how-to-custom-voice.md)
* [Parola chiave personalizzata](custom-keyword-overview.md)

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](language-support.md)).

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Altre informazioni sui comandi personalizzati](custom-commands.md)
* [Scopri di più su Direct Line Speech](direct-line-speech.md)
* [Ottenere Speech SDK](speech-sdk.md)