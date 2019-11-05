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
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507737"
---
# <a name="about-voice-assistants"></a>Informazioni sugli assistenti vocali

Gli assistenti vocali che usano servizi di riconoscimento vocale di Azure consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per le proprie applicazioni ed esperienze.

Il servizio Voice Assistant fornisce un'interazione rapida e affidabile tra un dispositivo e un'implementazione di Assistant che usa (1) il canale di sintesi vocale diretta di bot Framework o (2) il servizio integrato di comandi personalizzati (anteprima) per il completamento delle attività.

Le applicazioni si connettono al servizio Voice Assistant con Speech Software Development Kit (SDK).

   ![Diagramma concettuale del flusso del servizio di orchestrazione di Assistente vocale](media/voice-assistants/overview.png "Il flusso di Assistente vocale")

## <a name="core-features"></a>Funzionalità di base

| Categoria | Funzionalità |
|----------|----------|
|[Parola chiave Custom](speech-devices-sdk-create-kws.md) | Gli utenti possono avviare conversazioni con gli assistenti con una parola chiave personalizzata, ad esempio "Hey contoso". Un'app esegue questa operazione con un motore di parole chiave personalizzato nell'SDK di riconoscimento vocale, che può essere configurato con una parola chiave personalizzata [che è possibile generare qui](speech-devices-sdk-create-kws.md). Gli assistenti vocali possono usare la verifica delle parole chiave sul lato servizio per migliorare l'accuratezza dell'attivazione della parola chiave (rispetto al dispositivo da solo).
|[Riconoscimento vocale](speech-to-text.md) | Gli assistenti vocali convertono l'audio in tempo reale in testo [riconosciuto usando i servizi vocali di Azure](speech-to-text.md) . Questo testo è disponibile, in quanto viene trascritto, sia per l'implementazione dell'assistente che per l'applicazione client.
|[Sintesi vocale](text-to-speech.md) | Le risposte testuali dell'Assistente vengono sintetizzate tramite sintesi [vocale](text-to-speech.md) da servizi vocali di Azure. Questa sintesi viene quindi resa disponibile all'applicazione client come flusso audio. Microsoft offre la possibilità di creare una voce personalizzata neurale e di alta qualità che fornisce una voce al proprio marchio. Per altre informazioni, [Contattaci](mailto:mstts@microsoft.com).

## <a name="comparing-assistant-solutions"></a>Confronto tra soluzioni di assistente

Il servizio Voice Assistant connette l'applicazione on-device all'implementazione dell'assistente univoco. Gli sviluppatori possono creare assistenti vocali usando (1) il canale di [sintesi vocale diretta](direct-line-speech.md) di bot Framework o (2) la soluzione [comandi personalizzati (anteprima)](custom-commands.md) .

   ![Confronto tra soluzioni di assistente](media/voice-assistants/assistant-solution-comparison.png "Confronto tra soluzioni di assistente")

| Soluzione | Funzionalità |
|----------|----------|
|[Comandi personalizzati (anteprima)](custom-commands.md) | Custom Commands (anteprima) offre una soluzione di creazione e hosting semplificata per gli assistenti vocali. È adattato alle esigenze di completamento delle attività e degli scenari di comando e controllo.
|[Sintesi vocale diretta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Direct Line Speech consente una connessione semplice e uniforme tra (1) l'applicazione client, (2) un bot compatibile e (3) le funzionalità dei servizi di riconoscimento vocale di Azure. Per ulteriori informazioni sulla configurazione del bot per l'utilizzo del canale di riconoscimento vocale diretto, vedere [la relativa pagina nella documentazione di bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

Dopo aver creato un assistente vocale con una di queste soluzioni, connettere l'applicazione sul dispositivo usando il `DialogServiceConnector` nell'SDK di riconoscimento vocale. Per altri dettagli, vedere le guide introduttive ed esempi per ogni soluzione.

## <a name="getting-started-with-voice-assistants"></a>Introduzione agli assistenti vocali

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive per Assistente vocale, organizzate in base alla lingua.

| Guida introduttiva | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente vocale è disponibile su GitHub. Questi esempi coprono l'applicazione client per la connessione all'assistente in diversi linguaggi di programmazione più diffusi.

* [Esempi di Assistente vocale (SDK)](https://aka.ms/csspeech/samples)
* [Esercitazione: abilitare l'Assistente vocale con l'SDK di riconoscimento vocale,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Esercitazione

Esercitazione su come abilitare l' [Assistente vocale usando l'SDK di riconoscimento vocale e il canale di sintesi vocale diretta](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati usando i servizi di riconoscimento vocale di Azure possono usare la gamma completa di opzioni di personalizzazione disponibili per la selezione [vocale](speech-to-text.md) [, la sintesi vocale e](text-to-speech.md)la selezione di [parole chiave personalizzate](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
* [Ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
* [Altre informazioni sui comandi personalizzati (anteprima)](custom-commands.md)
* [Scopri di più su Direct Line Speech](direct-line-speech.md)