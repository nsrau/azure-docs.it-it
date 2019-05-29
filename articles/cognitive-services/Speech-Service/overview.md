---
title: Informazioni sui servizi Voce di Azure
titleSuffix: Azure Cognitive Services
description: I servizi Voce di Azure combinano il riconoscimento vocale, la sintesi vocale e la traduzione vocale in una singola sottoscrizione di Azure. Con Speech SDK, Speech Devices SDK o API REST è facile aggiungere il riconoscimento vocale ad applicazioni, strumenti e dispositivi. Aggiungere funzionalità di riconoscimento vocale in un chatbot esistente, convertire la sintesi vocale in un'applicazione di conversione o trascrivere grandi volumi di dati del call center.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: dc0871c0b16246fa178072e9d9f266d4c02ba1c4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003000"
---
# <a name="what-are-the-speech-services"></a>Informazioni sui servizi Voce

I servizi Voce di Azure combinano il riconoscimento vocale, la sintesi vocale e la traduzione vocale in una singola sottoscrizione di Azure. Con [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart) o [API REST](rest-apis.md) è facile abilitare il riconoscimento vocale in applicazioni, strumenti e dispositivi.

> [!IMPORTANT]
> I servizi Voce hanno sostituito i servizi API riconoscimento vocale Bing, Traduzione vocale e Riconoscimento vocale personalizzato. Vedere *Guide alle procedure > Migrazione* per istruzioni relative alla migrazione.

Queste funzionalità costituiscono i servizi Voce di Azure. Per altre informazioni sui casi d'uso comuni per ogni funzionalità, usare i collegamenti in questa tabella o esplorare il riferimento API.

| Service | Funzionalità | DESCRIZIONE | SDK | REST |
|---------|---------|-------------|-----|------|
| [Riconoscimento vocale](speech-to-text.md) | Riconoscimento vocale | Riconoscimento vocale trascrive in tempo reale flussi audio in testo da usare o visualizzare in applicazioni, dispositivi o strumenti. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Trascrizione Batch](batch-transcription.md) | Trascrizione batch consente la trascrizione asincrona della voce in testo scritto di grandi volumi di dati. Si tratta di un servizio basato su REST che usa lo stesso endpoint come personalizzazione e gestione modelli. | No  | [Sì](https://westus.cris.ai/swagger/ui/index) |
| | [Trascrizione conversazione](conversation-transcription-service.md) | Abilita il riconoscimento vocale in tempo reale, l'identificazione voce e la diarizzazione. È ideale per la trascrizione di riunioni dal vivo grazie alla capacità di distinguere i parlanti. | Sì | No  |
| | [Creare modelli conversione voce/testo personalizzati](#customize-your-speech-experience) | Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore. | No  | [Sì](https://westus.cris.ai/swagger/ui/index) |
| [Sintesi vocale](text-to-speech.md) | Sintesi vocale | Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano. È possibile scegliere tra voci standard e voci neurali (vedere [Supporto per le lingue](language-support.md)). | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Creare voci personalizzate](#customize-your-speech-experience) | Creazione di caratteri voce personalizzati univoci per il proprio marchio o prodotto. | No  | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduzione vocale](speech-translation.md) | Traduzione vocale | La traduzione vocale consente di attivare la traduzione vocale end-to-end in tempo reale e in più lingue in applicazioni, strumenti e dispositivi. È possibile usare questo servizio per il riconoscimento vocale e la traduzione vocale. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No  |
| [Assistenti virtuali voice-first](voice-first-virtual-assistants.md) | Assistenti virtuali voice-first | Gli assistenti virtuali personalizzati che usano i servizi Voce di Azure consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per applicazioni ed esperienze. Il canale Direct Line Speech di Bot Framework migliora tali funzionalità offrendo un punto di ingresso coordinato e orchestrato a un bot compatibile che abilita un'interazione vocale in ingresso e in uscita caratterizzata da una bassa latenza e da un'elevata affidabilità. | [Sì](voice-first-virtual-assistants.md) | No  |

## <a name="news-and-updates"></a>Notizie e aggiornamenti

Informazioni sulle novità relative ai servizi Voce di Azure.

* Maggio 2019 - Disponibilità della documentazione per [Trascrizione conversazione](conversation-transcription-service.md), [Trascrizione di call center](call-center-transcription.md) e [Assistenti virtuali voice-first](voice-first-virtual-assistants.md).
* Maggio 2019
    * Rilasciato Speech SDK 1.5.1. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
    * Rilasciato Speech SDK 1.5.0. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
* Aprile 2019 - Rilasciato Speech SDK 1.4.0 con il supporto per la sintesi vocale (beta) per C++, C# e Java su Windows e Linux. Inoltre, l'SDK ora supporta formati audio MP3 e Opus/Ogg per C++ e C# su Linux. Per un elenco completo di aggiornamenti, miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
* Marzo 2019 - È ora disponibile un nuovo endpoint per la sintesi vocale che restituisce un elenco completo delle voci disponibili in aree specifiche. Inoltre, sono ora supportate nuove aree per la sintesi vocale. Per altre informazioni, vedere le [informazioni di riferimento sull'API di sintesi vocale (REST)](rest-text-to-speech.md).

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
* [Esercitazione: Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare il testo tradotto in parlato, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Ottenere il codice di esempio

Il codice di esempio è disponibile in GitHub per ognuno dei servizi Voce di Azure. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

* [Esempi di riconoscimento vocale, sintesi vocale e traduzione vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
* [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="customize-your-speech-experience"></a>Personalizzare l'esperienza di riconoscimento vocale

I servizi Voce di Azure funzionano bene con i modelli predefiniti, tuttavia, è possibile personalizzare e ottimizzare l'esperienza per un prodotto o ambiente. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio. Dopo aver creato un modello personalizzato, è possibile usarlo con uno qualsiasi dei servizi Voce di Azure.

| Servizio di riconoscimento vocale | Modello | DESCRIZIONE |
|----------------|-------|-------------|
| Riconoscimento vocale | [Modello acustico](how-to-customize-acoustic-models.md) | Creazione di un modello acustico personalizzato per applicazioni, strumenti o dispositivi usati in ambienti particolari, ad esempio in un'automobile o in fabbrica, ognuno con condizioni di registrazione specifiche. Sono esempi di queste situazioni un eloquio con un forte accento, particolari rumori di sottofondo o l'uso di uno speciale microfono per la registrazione. |
| | [Modello linguistico](how-to-customize-language-model.md) | Creazione di un modello linguistico personalizzato per migliorare la trascrizione di grammatica e terminologia specifica di settore, ad esempio terminologia medica o IT. |
| | [Modello di pronuncia](how-to-customize-pronunciation.md) | Con un modello di pronuncia personalizzato è possibile definire la forma fonetica e visualizzare una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve è un file di pronunce, un semplice file con estensione TXT. |
| Sintesi vocale | [Carattere voce](how-to-customize-voice-font.md) | I caratteri voce personalizzati consentono di creare una voce distintiva e unica per il proprio marchio. Per iniziare è necessaria solo una piccola quantità di dati. Più dati si forniscono, più naturale e umano risulterà il carattere voce. |

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
