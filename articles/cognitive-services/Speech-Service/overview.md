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
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: b536c55b0f7c29bc463781d9fda0348ef994c5ce
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662225"
---
# <a name="what-are-the-speech-services"></a>Informazioni sui servizi Voce

I servizi Voce di Azure combinano il riconoscimento vocale, la sintesi vocale e la traduzione vocale in una singola sottoscrizione di Azure. Con [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](speech-devices-sdk-qsg.md) o [API REST](rest-apis.md) è facile abilitare il riconoscimento vocale in applicazioni, strumenti e dispositivi.

> [!IMPORTANT]
> I servizi Voce hanno sostituito i servizi API riconoscimento vocale Bing, Traduzione vocale e Riconoscimento vocale personalizzato. Vedere *Guide alle procedure > Migrazione* per istruzioni relative alla migrazione.

Queste funzionalità costituiscono i servizi Voce di Azure. Per altre informazioni sui casi d'uso comuni per ogni funzionalità, usare i collegamenti in questa tabella o esplorare il riferimento API.

| Service | Funzionalità | DESCRIZIONE | SDK | REST |
|---------|---------|-------------|-----|------|
| [Riconoscimento vocale](speech-to-text.md) | Riconoscimento vocale | Riconoscimento vocale trascrive in tempo reale flussi audio in testo da usare o visualizzare in applicazioni, dispositivi o strumenti. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Trascrizione Batch](batch-transcription.md) | Trascrizione batch consente la trascrizione asincrona della voce in testo scritto di grandi volumi di dati. Si tratta di un servizio basato su REST che usa lo stesso endpoint come personalizzazione e gestione modelli. | No  | [Sì](https://westus.cris.ai/swagger/ui/index) |
| | [Personalizzazione](#customize-your-speech-experience) | Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore. | No  | [Sì](https://westus.cris.ai/swagger/ui/index) |
| [Sintesi vocale](text-to-speech.md) | Sintesi vocale | Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano. È possibile scegliere tra voci standard e voci neurali (vedere [Supporto per le lingue](language-support.md)). | No  | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Personalizzazione](#customize-your-speech-experience) | Creazione di caratteri voce personalizzati univoci per il proprio marchio o prodotto. | No  | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduzione vocale](speech-translation.md) | Traduzione vocale | La traduzione vocale consente di attivare la traduzione vocale end-to-end in tempo reale e in più lingue in applicazioni, strumenti e dispositivi. È possibile usare questo servizio per il riconoscimento vocale e la traduzione vocale. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No  |

## <a name="news-and-updates"></a>Notizie e aggiornamenti

Informazioni sulle novità relative ai servizi Voce di Azure.

* Marzo 2019 - È ora disponibile un nuovo endpoint per la sintesi vocale che restituisce un elenco completo delle voci disponibili in aree specifiche. Inoltre, sono ora supportate nuove aree per la sintesi vocale. Per altre informazioni, vedere le [informazioni di riferimento sull'API di sintesi vocale (REST)](rest-text-to-speech.md).
* Febbraio 2019 - Rilasciato Speech SDK 1.3.0 con supporto per [Unity (beta)](quickstart-csharp-unity.md). Aggiunta del supporto per la classe `AudioInput`, che consente di scegliere l'origine di flusso per l'audio. Per un elenco completo di miglioramenti e problemi noti, vedere [Note sulla versione](releasenotes.md).
* Dicembre 2018 - Rilasciato Speech SDK 1.2.0 con supporto per [Python](quickstart-python.md) e [Node. js](quickstart-js-node.md), nonché Ubuntu 18.04 LTS. Per altre informazioni, vedere [Note sulla versione](releasenotes.md).

## <a name="try-speech-services"></a>Provare i servizi Voce

Sono disponibili modelli di avvio rapido nei linguaggi di programmazione più diffusi, ognuno progettato per consentire all'utente di disporre dell'esecuzione di codice in meno di 10 minuti. Questa tabella contiene i modelli di avvio rapido più popolari per ogni funzionalità. Usare il riquadro di spostamento a sinistra per esplorare altri linguaggi e piattaforme.

| Riconoscimento vocale (SDK) | Traduzione vocale (SDK) | Sintesi vocale (REST) |
|-------------------|-------------------|-----------------------|
| [C# .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows, Linux, macOS)](quickstart-python-text-to-speech.md) |
| [JavaScript (Browser)](quickstart-js-browser.md) | [C# .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#, .NET Core (Windows, Linux, macOS)](quickstart-dotnet-text-to-speech.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows, Linux, macOS)](quickstart-nodejs-text-to-speech.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

Dopo aver usato i servizi Voce, provare l'esercitazione che illustra come riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS.

* [Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS per C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="get-sample-code"></a>Ottenere il codice di esempio

Il codice di esempio è disponibile in GitHub per ognuno dei servizi Voce di Azure. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

* [Speech-to-text and speech translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk) (Esempi di riconoscimento vocale e traduzione vocale (SDK))
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
