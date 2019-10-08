---
title: Informazioni sui servizi Voce
titleSuffix: Azure Cognitive Services
description: I servizi Voce di Azure combinano riconoscimento vocale, sintesi vocale e traduzione vocale in un'unica sottoscrizione di Azure. Con Speech SDK, Speech Devices SDK o API REST è facile aggiungere il riconoscimento vocale ad applicazioni, strumenti e dispositivi. Aggiungere funzionalità di riconoscimento vocale in un chatbot esistente, convertire la sintesi vocale in un'applicazione di conversione o trascrivere grandi volumi di dati del call center.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 993064a36bd12c95e83ffb7c9635c3e4dfcec0f0
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803353"
---
# <a name="what-are-the-speech-services"></a>Informazioni sui servizi Voce

I servizi Voce di Azure combinano riconoscimento vocale, sintesi vocale e traduzione vocale in un'unica sottoscrizione di Azure. Con [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart) o [API REST](rest-apis.md) è facile abilitare il riconoscimento vocale in applicazioni, strumenti e dispositivi.

> [!IMPORTANT]
> I servizi Voce hanno sostituito i servizi API riconoscimento vocale Bing, Traduzione vocale e Riconoscimento vocale personalizzato. Vedere *Guide alle procedure > Migrazione* per istruzioni relative alla migrazione.

Queste funzionalità costituiscono i servizi Voce di Azure. Per altre informazioni sui casi d'uso comuni per ogni funzionalità, usare i collegamenti in questa tabella o esplorare il riferimento API.

| Service | Funzionalità | DESCRIZIONE | SDK | REST |
|---------|---------|-------------|-----|------|
| [Riconoscimento vocale](speech-to-text.md) | Riconoscimento vocale | Riconoscimento vocale trascrive in tempo reale flussi audio in testo da usare o visualizzare in applicazioni, dispositivi o strumenti. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Trascrizione Batch](batch-transcription.md) | Trascrizione batch consente la trascrizione asincrona della voce in testo scritto di grandi volumi di dati. Si tratta di un servizio basato su REST che usa lo stesso endpoint come personalizzazione e gestione modelli. | No | [Sì](https://westus.cris.ai/swagger/ui/index) |
| | [Trascrizione conversazione](conversation-transcription-service.md) | Abilita il riconoscimento vocale in tempo reale, l'identificazione voce e la diarizzazione. È ideale per la trascrizione di riunioni dal vivo grazie alla capacità di distinguere i parlanti. | Sì | No |
| | [Creare modelli conversione voce/testo personalizzati](#customize-your-speech-experience) | Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore. | No | [Sì](https://westus.cris.ai/swagger/ui/index) |
| [Sintesi vocale](text-to-speech.md) | Sintesi vocale | Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano usando [Speech Synthesis Markup Language (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). È possibile scegliere tra voci standard e voci neurali (vedere [Supporto per le lingue](language-support.md)). | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Creare voci personalizzate](#customize-your-speech-experience) | Creazione di caratteri voce personalizzati univoci per il proprio marchio o prodotto. | No | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduzione vocale](speech-translation.md) | Traduzione vocale | La traduzione vocale consente di attivare la traduzione vocale end-to-end in tempo reale e in più lingue in applicazioni, strumenti e dispositivi. È possibile usare questo servizio per il riconoscimento vocale e la traduzione vocale. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No |
| [Assistenti virtuali voice-first](voice-first-virtual-assistants.md) | Assistenti virtuali voice-first | Gli assistenti virtuali personalizzati che usano i servizi Voce di Azure consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per applicazioni ed esperienze. Il canale Direct Line Speech di Bot Framework migliora tali funzionalità offrendo un punto di ingresso coordinato e orchestrato a un bot compatibile che abilita un'interazione vocale in ingresso e in uscita caratterizzata da una bassa latenza e da un'elevata affidabilità. | [Sì](voice-first-virtual-assistants.md) | No |

## <a name="news-and-updates"></a>Notizie e aggiornamenti

Informazioni sulle novità relative ai servizi Voce di Azure.

* Settembre 2019
  * Rilasciato Speech SDK 1.7.0. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
* Agosto 2019
  * **Nuova esercitazione**: [Abilitare la voce per il bot con Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Aggiunta di un nuovo modo di parlare, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles), per la voce `en-US-JessaNeural`. 
* Giugno 2019
  * Rilasciato Speech SDK 1.6.0. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
* Maggio 2019 - Disponibilità della documentazione per [Trascrizione conversazione](conversation-transcription-service.md), [Trascrizione di call center](call-center-transcription.md) e [Assistenti virtuali voice-first](voice-first-virtual-assistants.md).
* Maggio 2019
  * Rilasciato Speech SDK 1.5.1. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
  * Rilasciato Speech SDK 1.5.0. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).

## <a name="try-speech-services"></a>Provare i servizi Voce

Sono disponibili modelli di avvio rapido nei linguaggi di programmazione più diffusi, ognuno progettato per consentire all'utente di disporre dell'esecuzione di codice in meno di 10 minuti. Questa tabella contiene i modelli di avvio rapido più popolari per ogni funzionalità. Usare il riquadro di spostamento a sinistra per esplorare altri linguaggi e piattaforme.

| Riconoscimento vocale (SDK) | API Sintesi vocale (SDK) | Traduzione vocale (SDK) |
|----------------------|----------------------|-------------------|
| [C# .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (Browser)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C# .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Per il riconoscimento vocale e la sintesi vocale sono disponibili anche endpoint REST e guide di avvio rapido associate.

Dopo aver usato i servizi Voce, provare l'esercitazione che illustra come riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS.

* [Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS per C#](how-to-recognize-intents-from-speech-csharp.md)
* [Esercitazione: Abilitare il servizio voce per il bot con Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Esercitazione: Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare il testo tradotto in parlato, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Ottenere il codice di esempio

Il codice di esempio è disponibile in GitHub per ognuno dei servizi Voce di Azure. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

* [Esempi di riconoscimento vocale, sintesi vocale e traduzione vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
* [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))
* [Esempi di assistente virtuale basati sul riconoscimento vocale (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalizzare l'esperienza di riconoscimento vocale

I servizi Voce di Azure funzionano bene con i modelli predefiniti, tuttavia, è possibile personalizzare e ottimizzare l'esperienza per un prodotto o ambiente. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio. Dopo aver creato un modello personalizzato, è possibile usarlo con uno qualsiasi dei servizi Voce di Azure.

| Servizio di riconoscimento vocale | Piattaforma | DESCRIZIONE |
|----------------|-------------|-------------|
| Riconoscimento vocale | [Riconoscimento vocale personalizzato](https://aka.ms/customspeech) | Personalizzare i modelli di riconoscimento vocale in base alle esigenze e ai dati disponibili. Superare gli ostacoli al riconoscimento vocale, come il modo di parlare, il vocabolario e il rumore di fondo. |
| Sintesi vocale | [Voce personalizzata](https://aka.ms/customvoice) | Creare un'unica voce riconoscibile per le app di sintesi vocale con i dati della lingua disponibili. È possibile ottimizzare ulteriormente gli output vocali regolando un set di parametri vocali. |

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
