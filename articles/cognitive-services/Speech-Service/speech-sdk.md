---
title: Informazioni sul servizio riconoscimento vocale (Speech SDK)
titleSuffix: Azure Cognitive Services
description: Il Software Development Kit vocale (SDK) espone molte delle funzionalità del servizio vocale, semplificando lo sviluppo di applicazioni abilitate per la sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 9356cbb6eb210d6745d90e8ad001917e73592cd3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96021988"
---
# <a name="about-the-speech-sdk"></a>Informazioni su Speech SDK

Il Software Development Kit vocale (SDK) espone molte delle funzionalità del servizio vocale, per consentire lo sviluppo di applicazioni abilitate per la sintesi vocale. Speech SDK è disponibile in molti linguaggi di programmazione e in tutte le piattaforme.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Funzionalità dello scenario

Speech SDK espone numerose funzionalità del servizio di riconoscimento vocale, ma non tutte. Le funzionalità dell'SDK per i dialoghi sono spesso associate agli scenari. Speech SDK è ideale per scenari in tempo reale e non in tempo reale, usando dispositivi locali, file, archiviazione BLOB di Azure e anche flussi di input e output. Quando uno scenario non è realizzabile con l'SDK di riconoscimento vocale, cercare un'API REST alternativa.

### <a name="speech-to-text"></a>Riconoscimento vocale

Il riconoscimento [vocale (noto](speech-to-text.md) anche come *riconoscimento vocale*) consente di trascrivere flussi audio in testo che le applicazioni, gli strumenti o i dispositivi possono utilizzare o visualizzare. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](../luis/index.yml) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. Usare la [traduzione vocale](speech-translation.md) per tradurre l'input vocale in una lingua diversa con una singola chiamata. Per altre informazioni, vedere [nozioni di base su sintesi vocale](./get-started-speech-to-text.md).

Il **riconoscimento vocale (SR), l'elenco di frasi, il preventivo, la traduzione e i contenitori locali** sono disponibili nelle piattaforme seguenti:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & UWP & Unity & Novell & Linux & macOS
  - Java (JRE e Android)
  - JavaScript (sopracciglia e NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (solo SR)

### <a name="text-to-speech"></a>Sintesi vocale

Il testo [da](text-to-speech.md) sintesi vocale (anche noto come *sintesi vocale*) converte il testo in un riconoscimento vocale sintetizzato da uomo. Il testo di input è costituito da valori letterali stringa o mediante [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). Per ulteriori informazioni sulle voci standard o neurali, vedere [linguaggio di sintesi vocale e supporto vocale](language-support.md#text-to-speech).

La **voce sintesi vocale** è disponibile nelle piattaforme seguenti:

  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE e Android)
  - Python
  - Swift
  - Objective-C
  - L'API REST TTS può essere usata in qualsiasi altra situazione.

### <a name="voice-assistants"></a>Assistenti vocali

Gli [assistenti vocali](voice-assistants.md) che usano l'SDK di riconoscimento vocale consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per le proprie applicazioni ed esperienze. Il servizio Voice Assistant fornisce un'interazione rapida e affidabile tra un dispositivo e un assistente. L'implementazione usa il canale di sintesi vocale diretta di bot Framework o il servizio integrato di comandi personalizzati (anteprima) per il completamento delle attività. Inoltre, gli assistenti vocali possono usare voci personalizzate create nel [portale vocale personalizzato](https://aka.ms/customvoice) per aggiungere un'esperienza di output vocale univoca.

Gli **assistenti vocali** sono disponibili nelle piattaforme seguenti:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)

#### <a name="keyword-spotting"></a>Individuazione di parole chiave

Il concetto di [individuazione delle parole chiave](./custom-keyword-basics.md) è supportato nell'SDK di riconoscimento vocale. L'individuazione di parole chiave è l'azione di identificazione di una parola chiave in sintesi vocale, seguita da un'azione dopo aver ascoltato la parola chiave. Ad esempio, "Hey Cortana" attiverà Cortana Assistant.

L' **individuazione delle parole chiave (KWS)** è disponibile nelle piattaforme seguenti:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - La funzionalità di individuazione delle parole chiave (KWS) può funzionare con qualsiasi tipo di microfono, il supporto ufficiale di KWS, tuttavia, è attualmente limitato agli array di microfoni presenti nell'hardware Kinect DK di Azure o all'SDK per dispositivi vocali

### <a name="meeting-scenarios"></a>Scenari di riunione

Speech SDK è perfetto per la trascrizione di scenari di riunioni, sia da un singolo dispositivo che da una conversazione su più dispositivi.

#### <a name="conversation-transcription"></a>Trascrizione conversazione

La [trascrizione delle conversazioni](conversation-transcription.md) consente il riconoscimento vocale in tempo reale (e asincrono), l'identificazione del relatore e l'attribuzione di frasi a ogni altoparlante (anche noto come " *diare*"). È ideale per la trascrizione di riunioni dal vivo grazie alla capacità di distinguere i parlanti.

La **trascrizione delle conversazioni** è disponibile nelle piattaforme seguenti:

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)

#### <a name="multi-device-conversation"></a>Conversazione per più dispositivi

Con la conversazione per più [dispositivi](multi-device-conversation.md), Connetti più dispositivi o client in una conversazione per inviare messaggi basati su sintesi vocale o basati su testo, con supporto semplificato per la trascrizione e la traduzione.

La conversazione su più **dispositivi** è disponibile nelle piattaforme seguenti:

  - /Windows C++
  - C# (Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Scenari Custom/Agent

Speech SDK può essere usato per la trascrizione di scenari di Call Center, in cui vengono generati i dati di telefonia.

#### <a name="call-center-transcription"></a>Trascrizioni di call center

La [trascrizione del Call Center](call-center-transcription.md) è uno scenario comune per la traduzione vocale per la trascrizione di grandi volumi di dati di telefonia che possono provenire da diversi sistemi, ad esempio IVR (Interactive Voice Response). I modelli di riconoscimento vocale più recenti del servizio di riconoscimento vocale sono in fase di trascrizione di questi dati di telefonia, anche nei casi in cui i dati sono difficili da comprendere per un uomo.

La **trascrizione del Call Center** è disponibile tramite il servizio batch Speech tramite l'API REST e può essere usata in qualsiasi situazione.

### <a name="codec-compressed-audio-input"></a>Input audio compresso codec

Molti dei linguaggi di programmazione dell'SDK vocale supportano i flussi di input audio compressi di codec. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">usare formati <span class="docon docon-navigate-external x-hidden-focus"></span> di input audio compressi </a>.

L' **input audio compresso codec** è disponibile nelle piattaforme seguenti:

  - /Linux C++
  - /Linux C#
  - Java/Linux, Android e iOS

## <a name="rest-api"></a>API REST

Mentre l'SDK per la sintesi vocale copre molte funzionalità del servizio di riconoscimento vocale, per alcuni scenari potrebbe essere necessario usare l'API REST.

### <a name="batch-transcription"></a>Trascrizione Batch

La [trascrizione batch](batch-transcription.md) consente la trascrizione asincrona di sintesi vocale di grandi volumi di dati. La trascrizione batch è possibile solo dall'API REST. Oltre a convertire l'audio vocale in testo, batch speech-to-text consente anche di eseguire l'analisi e i sentimenti.

## <a name="customization"></a>Personalizzazione

Il servizio di riconoscimento vocale fornisce funzionalità eccezionali con i modelli predefiniti, tra sintesi vocale, sintesi vocale e traduzione vocale. In alcuni casi potrebbe essere necessario aumentare le prestazioni di base per lavorare ancora meglio con il caso d'uso univoco. Il servizio di riconoscimento vocale offre un'ampia gamma di strumenti di personalizzazione senza codice che semplificano e consentono di creare un vantaggio competitivo con modelli personalizzati basati sui propri dati. Questi modelli saranno disponibili solo per l'utente e l'organizzazione.

### <a name="custom-speech-to-text"></a>Da Riconoscimento vocale personalizzato a testo

Quando si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, lingua e pronuncia personalizzati per risolvere il rumore di ambiente o il vocabolario specifico del settore. La creazione e la gestione di modelli di Riconoscimento vocale personalizzato senza codice sono disponibili tramite il [portale di riconoscimento vocale personalizzato](https://aka.ms/customspeech). Una volta pubblicato, il modello di Riconoscimento vocale personalizzato può essere usato dall'SDK di riconoscimento vocale.

### <a name="custom-text-to-speech"></a>Sintesi vocale personalizzata

Una sintesi vocale personalizzata, nota anche come personalizzata, è costituita da un set di strumenti online che ti permettono di creare una voce unica e riconoscibile per il tuo marchio. La creazione e la gestione di modelli vocali personalizzati senza codice sono disponibili tramite il [portale vocale personalizzato](https://aka.ms/customvoice). Una volta pubblicato, il modello Voice personalizzato può essere usato dall'SDK di riconoscimento vocale.

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

* [Creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)