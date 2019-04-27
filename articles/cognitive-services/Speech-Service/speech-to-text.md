---
title: Per il riconoscimento vocale con servizi di riconoscimento vocale di Azure
titleSuffix: Azure Cognitive Services
description: Per il riconoscimento vocale da servizi di riconoscimento vocale di Azure, noto anche come per il riconoscimento vocale, la trascrizione in tempo reale consente di flussi audio in testo che possono utilizzare le applicazioni, dispositivi o gli strumenti, visualizzare e agire come input del comando. Questo servizio si basa sulla stessa tecnologia di riconoscimento che Microsoft Usa per i prodotti Cortana e Office e interagisce senza problemi con la conversione e la sintesi vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9e6bc1264e668ba5c6593ce36e721f54e685c391
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461256"
---
# <a name="what-is-speech-to-text"></a>Che cos'è riconoscimento vocale?

Per il riconoscimento vocale da servizi di riconoscimento vocale di Azure, noto anche come per il riconoscimento vocale, la trascrizione in tempo reale consente di flussi audio in testo che possono utilizzare le applicazioni, dispositivi o gli strumenti, visualizzare e agire come input del comando. Questo servizio si basa sulla stessa tecnologia di riconoscimento che Microsoft Usa per i prodotti Cortana e Office e interagisce senza problemi con la conversione e la sintesi vocale.  Per un elenco completo delle lingue disponibili per il riconoscimento vocale, vedere [lingue supportate](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support#speech-to-text).

Per impostazione predefinita, il servizio riconoscimento vocale Usa il modello di linguaggio universale. Questo modello è stato eseguito il training usando i dati di proprietà di Microsoft ed è distribuito nel cloud. È ottimale per la conversazione e gli scenari di dettatura. Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore. 

È facilmente possibile acquisire audio dal microfono, leggere da un flusso o accedere ai file audio da un archivio con Speech SDK e API REST. Speech SDK supporta WAV/PCM 16 bit, kHz 16, singolo canale audio per il riconoscimento vocale. Formati audio aggiuntivi sono supportati tramite il [endpoint REST per il riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) o nella [trascrizione servizio batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Funzionalità di base

Ecco le funzionalità disponibili attraverso il sistema Speech SDK e API REST:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Trascrivi utterances breve (< 15 secondi). Supporta solo il risultato finale di trascrizione. | Sì | Sì |
| Trascrizione continua di espressioni lungo e streaming audio (> 15 secondi). Supporta i risultati di trascrizione intermedie e finali. | Sì | No  |
| Derivare i risultati di riconoscimento con finalità [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Sì | No\* |
| Batch in modo asincrono la trascrizione di file audio. | No  | Sì\** |
| Creare e gestire modelli di riconoscimento vocale. | No  | Sì\** |
| Creare e gestire le distribuzioni di modelli personalizzato. | No  | Sì\** |
| Creare test di accuratezza per misurare l'accuratezza del modello di previsione e i modelli personalizzati. | No  | Sì\** |
| Gestire le sottoscrizioni. | No  | Sì\** |

\* *Le finalità e le entità LUIS possono essere derivate con una sottoscrizione di LUIS separata. Con questa sottoscrizione, il SDK può chiamare LUIS per l'utente e fornire entità e finalità dei risultati. Con l'API REST, è possibile chiamare LUIS manualmente per derivare finalità ed entità con la sottoscrizione di LUIS.*

\** *Questi servizi sono disponibili tramite l'endpoint cris.ai. Visualizzare [Swagger riferimento](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Introduzione al riconoscimento vocale

Sono disponibili modelli di avvio rapido nei linguaggi di programmazione più diffusi, ognuno progettato per consentire all'utente di disporre dell'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco completo delle guide introduttive di Speech SDK organizzati per linguaggio.

| Guida introduttiva | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| [C#, .NET core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript, Browser](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tr)  |

Se si preferisce usare il servizio REST per il riconoscimento vocale, vedere [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Esercitazioni e codice di esempio

Dopo aver usato i servizi Voce, provare l'esercitazione che illustra come riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS.

* [Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS per C#](how-to-recognize-intents-from-speech-csharp.md)

Codice di esempio per Speech SDK è disponibile in GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati.

* [Esempi di riconoscimento vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))

## <a name="customization"></a>Personalizzazione

Oltre al modello Universal utilizzato dai servizi di riconoscimento vocale, è possibile creare modelli acustici, lingua e pronuncia personalizzati specifici alla tua esperienza. Ecco un elenco di opzioni di personalizzazione:

| Modello | DESCRIZIONE |
|-------|-------------|
| [Modello acustico](how-to-customize-acoustic-models.md) | Creazione di un modello acustico personalizzato è utile se le applicazioni, dispositivi o gli strumenti vengono usati in un ambiente specifico, ad esempio in un'automobile o una factory con le condizioni di registrazione specifiche. Sono esempi di queste situazioni un eloquio con un forte accento, particolari rumori di sottofondo o l'uso di uno speciale microfono per la registrazione. |
| [Modello linguistico](how-to-customize-language-model.md) | Creare un modello di lingua personalizzati per migliorare la trascrizione di vocabolario specifici del settore e grammatica, ad esempio la terminologia mediche o gergo IT. |
| [Modello di pronuncia](how-to-customize-pronunciation.md) | Con un modello di pronuncia personalizzato è possibile definire la forma fonetica e visualizzare una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve è un file di pronunce, un semplice file con estensione TXT. |

> [!NOTE]
> Le opzioni di personalizzazione variano a seconda lingua/impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="migration-guides"></a>Guide alla migrazione

> [!WARNING]
> Riconoscimento vocale Bing verrà ritirato il 15 ottobre 2019.

Se le applicazioni, strumenti o prodotti Usa l'API riconoscimento vocale Bing o riconoscimento vocale personalizzato, abbiamo creato le guide per la migrazione a servizi di riconoscimento vocale.

* [Eseguire la migrazione da riconoscimento vocale Bing per i servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Eseguire la migrazione da riconoscimento vocale personalizzato per i servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
* [Ottenere il SDK di riconoscimento vocale](speech-sdk.md)
