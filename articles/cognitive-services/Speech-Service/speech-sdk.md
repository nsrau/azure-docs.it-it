---
title: Informazioni su Speech SDK - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il Kit di sviluppo software di riconoscimento vocale (SDK) espone molte delle funzionalità del servizio di riconoscimento vocale, semplificando lo sviluppo di applicazioni abilitate per il riconoscimento vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a2ff4a94c1b2941f645cd7032ef476d33dffdb00
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656652"
---
# <a name="about-the-speech-sdk"></a>Informazioni su Speech SDK

Il Kit di sviluppo software di riconoscimento vocale (SDK) espone molte delle funzionalità del servizio di riconoscimento vocale, per consentire lo sviluppo di applicazioni abilitate per il riconoscimento vocale. Speech SDK è disponibile in molti linguaggi di programmazione e in tutte le piattaforme.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Funzionalità dello scenario

Speech SDK espone molte funzionalità del servizio di riconoscimento vocale, ma non tutte. Le funzionalità di Speech SDK sono spesso associate agli scenari. Speech SDK è ideale per scenari in tempo reale e non in tempo reale, usando dispositivi locali, file, archiviazione BLOB di Azure e persino flussi di input e output. Quando uno scenario non è realizzabile con Speech SDK, cercare un'alternativa all'API REST.

### <a name="speech-to-text"></a>Riconoscimento vocale

[La sintesi vocale](speech-to-text.md) (noto anche come *riconoscimento vocale*) trascrive i flussi audio in testo che le applicazioni, gli strumenti o i dispositivi possono utilizzare o visualizzare. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](../luis/index.yml) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. Usa [la traduzione vocale](speech-translation.md) per tradurre l'input vocale in una lingua diversa con una singola chiamata. Per ulteriori informazioni, consultate Nozioni di base sulla [sintesi](speech-to-text-basics.md)vocale.

### <a name="text-to-speech"></a>Sintesi vocale

[La sintesi vocale](text-to-speech.md) (nota anche come *sintesi vocale*) converte il testo in un discorso sintetizzato simile a quello umano. Il testo di input è valori letterali stringa o tramite [il linguaggio SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Per ulteriori informazioni sulle voci standard o neurali, vedere Supporto vocale e lingua [di sintesi vocale](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Assistenti vocali

Gli assistenti vocali che utilizzano l'SDK di riconoscimento vocale consentono agli sviluppatori di creare interfacce conversazionali naturali e simili a utenti per le loro applicazioni ed esperienze. Il servizio di assistente vocale fornisce un'interazione veloce e affidabile tra un dispositivo e un assistente. L'implementazione usa il canale Direct Line Speech di Bot Framework o il servizio integrato di comandi personalizzati (anteprima) per il completamento delle attività. Inoltre, è possibile creare assistenti vocali utilizzando il [portale vocale personalizzato](https://aka.ms/customvoice) per creare un'esperienza vocale unica.

#### <a name="keyword-spotting"></a>Avvio delle parole chiave

Il concetto di [spotting con parole chiave](speech-devices-sdk-create-kws.md) è supportato in Speech SDK. L'individuazione delle parole chiave è l'atto di identificare una parola chiave nel discorso, seguita da un'azione dopo aver ascoltato la parola chiave. Ad esempio, "Ehi Cortana" attiverà l'assistente Cortana.

### <a name="meeting-scenarios"></a>Scenari di riunione

Speech SDK è perfetto per la trascrizione di scenari di riunione, sia da un singolo dispositivo che da una conversazione multi-dispositivo.

#### <a name="conversation-transcription"></a>Trascrizione conversazione

[La trascrizione delle conversazioni](conversation-transcription.md) consente il riconoscimento vocale in tempo reale (e asincrono), l'identificazione dell'altoparlante e l'attribuzione della frase a ciascun oratore (nota anche come *diarizzazione).* È ideale per la trascrizione di riunioni dal vivo grazie alla capacità di distinguere i parlanti.

#### <a name="multi-device-conversation"></a>Conversazione per più dispositivi

Con [Multi-device Conversation](multi-device-conversation.md), collegare più dispositivi o client in una conversazione per inviare messaggi basati sul riconoscimento vocale o testuale, con un facile supporto per la trascrizione e la traduzione.

### <a name="custom--agent-scenarios"></a>Scenari personalizzati/agente

Speech SDK può essere utilizzato per la trascrizione di scenari di call center, in cui vengono generati dati di telefonia.

#### <a name="call-center-transcription"></a>Trascrizioni di call center

[La trascrizione](call-center-transcription.md) del Call Center è uno scenario comune per la sintesi vocale per la trascrizione di grandi volumi di dati di telefonia che possono provenire da vari sistemi, ad esempio Interactive Voice Response (IVR). Gli ultimi modelli di riconoscimento vocale del servizio di riconoscimento vocale eccellono nel trascrivere questi dati di telefonia, anche nei casi in cui i dati sono difficili da comprendere per un essere umano.

### <a name="codec-compressed-audio-input"></a>Ingresso audio compresso codec

Molti dei linguaggi di programmazione di Speech SDK supportano flussi di input audio compressi di codec. Per ulteriori informazioni, consultate <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">Utilizzare <span class="docon docon-navigate-external x-hidden-focus"> </span>formati </a>di input audio compressi.

## <a name="rest-api"></a>API REST

Mentre l'SDK vocale copre molte funzionalità del servizio di riconoscimento vocale, per alcuni scenari è possibile usare l'API REST. Ad esempio, la gestione degli endpoint viene esposta solo tramite l'API REST.

> [!TIP]
> Quando si utilizza l'API REST, usare l'editor Swagger per generare automaticamente le librerie client. Ad esempio, per generare una libreria client di trascrizione Batch.For example, to generate a Batch transcription client library.
> 1. Copia l'URL di esempio qui sotto:
>     ```http
>     https://westus.cris.ai/docs/v2.0/swagger
>     ```
> 1. Accedere <a href="https://editor.swagger.io/" target="_blank">all'editor <span class="docon docon-navigate-external x-hidden-focus"></span> Swagger</a>
> 1. Selezionare**URL di importazione** **file** > e incollare l'URL
> 1. Selezionare **Genera client** e scegliere il linguaggio di programmazione desiderato

### <a name="batch-transcription"></a>Trascrizione Batch

[La trascrizione batch](batch-transcription.md) consente la trascrizione asincrona vocale/sintesi testo di grandi volumi di dati. La trascrizione batch è possibile solo dall'API REST.

## <a name="customization"></a>Personalizzazione

Il servizio di riconoscimento vocale offre grandi funzionalità con i modelli predefiniti per la sintesi vocale, la sintesi vocale e la traduzione vocale. A volte potresti voler aumentare le prestazioni di base per lavorare ancora meglio con il tuo caso d'uso unico. Il servizio di riconoscimento vocale dispone di una varietà di strumenti di personalizzazione senza codice che semplificano la gestione e consentono di creare un vantaggio competitivo con modelli personalizzati basati sui propri dati. Questi modelli saranno disponibili solo per te e la tua organizzazione.

### <a name="custom-speech-to-text"></a>Sintesi vocale personalizzata

Quando si usa la sintesi vocale per il riconoscimento e la trascrizione in un ambiente unico, è possibile creare e addestrare modelli acustici, linguistici e di pronuncia personalizzati per risolvere il rumore ambientale o il vocabolario specifico del settore. La creazione e la gestione di modelli di riconoscimento vocale personalizzato senza codice sono disponibili tramite il [portale di riconoscimento vocale personalizzato](https://aka.ms/customspeech). Una volta pubblicato, il modello di riconoscimento vocale personalizzato può essere utilizzato dall'SDK di riconoscimento vocale.

### <a name="custom-text-to-speech"></a>Sintesi vocale personalizzata

La sintesi vocale personalizzata, nota anche come Custom Voice, è un insieme di strumenti online che ti consentono di creare una voce riconoscibile e unica nel suo genere per il tuo marchio. La creazione e la gestione di modelli vocali personalizzati senza codice sono disponibili tramite il [portale vocale personalizzato](https://aka.ms/customvoice). Una volta pubblicato, il modello Di voce personalizzata può essere utilizzato da Speech SDK.

## <a name="get-the-speech-sdk"></a>Ottenere Speech SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Browser](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
