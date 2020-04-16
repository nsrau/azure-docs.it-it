---
title: Sintesi vocale - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di sintesi vocale del servizio di riconoscimento vocale consente alle applicazioni, agli strumenti o ai dispositivi di convertire il testo in sintesi di tipo naturale. Scegli voci preimpostate o crea la tua voce personalizzata.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399510"
---
# <a name="what-is-text-to-speech"></a>Che cos'è la sintesi vocale?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

La sintesi vocale del servizio di riconoscimento vocale consente alle applicazioni, agli strumenti o ai dispositivi di convertire il testo in sintesi di tipo umano. Scegli tra voci standard e neurali o crea una voce personalizzata unica per il tuo prodotto o marchio. Sono disponibili oltre 75 voci standard in più di 45 lingue e impostazioni locali e 5 voci neurali sono disponibili in un numero selezionato di lingue e impostazioni locali. Per un elenco completo delle voci, delle lingue e delle impostazioni locali supportate, vedere [Lingue supportate.](language-support.md#text-to-speech)

> [!NOTE]
> Bing Speech è stato dismesso il 15 ottobre 2019. Se le applicazioni, gli strumenti o i prodotti usano le API di riconoscimento vocale Bing o il riconoscimento vocale personalizzato, sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.
> - [Eseguire la migrazione da Bing Speech al servizio di riconoscimento vocale](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Caratteristiche principali

* Sintesi vocale: usare [l'SDK vocale](quickstarts/text-to-speech-audio-file.md) o l'API [REST](rest-text-to-speech.md) per convertire la sintesi vocale usando voci standard, neurali o personalizzate.

* Sintesi asincrona di audio lungo: usare [l'API Audio lungo](long-audio-api.md) per sintetizzare in modo asincrono i file di sintesi vocale più lunghi di 10 minuti (ad esempio audiolibri o lezioni). A differenza della sintesi eseguita utilizzando l'SDK vocale o l'API REST da voce a testo, le risposte non vengono restituite in tempo reale. L'aspettativa è che le richieste vengano inviate in modo asincrono, che vengano sottoposte a polling le risposte e che l'audio sintetizzato venga scaricato quando reso disponibile dal servizio. Sono supportate solo voci neurali personalizzate.

* Voci standard - Create utilizzando tecniche di sintesi parametrica statistica e/o di sintesi della concatenazione. Queste voci sono altamente intelligibili e suono naturale. È possibile abilitare facilmente le applicazioni a parlare in più di 45 lingue, con una vasta gamma di opzioni vocali. Queste voci forniscono un'elevata precisione di pronuncia, incluso il supporto per abbreviazioni, espansioni di acronimi, interpretazioni data/ora, polifoni e altro ancora. Per un elenco completo delle voci standard, vedere [Lingue supportate.](language-support.md#text-to-speech)

* Voci neurali - Reti neurali profonde sono utilizzate per superare i limiti della sintesi vocale tradizionale per quanto riguarda lo stress e l'intonazione nella lingua parlata. La previsione della prosodia e la sintesi vocale vengono eseguite simultaneamente, il che si traduce in uscite più fluide e dal suono naturale. Le voci neurali possono essere utilizzate per rendere le interazioni con chatbot e assistenti vocali più naturali e coinvolgenti, convertire testi digitali come e-book in audiolibri e migliorare i sistemi di navigazione in auto. Con la prosodia naturale simile all'uomo e la chiara articolazione delle parole, le voci neurali riducono significativamente l'affaticamento dell'ascolto quando si interagisce con i sistemi di ia-A. Per un elenco completo delle voci neurali, vedere [lingue supportate](language-support.md#text-to-speech).

* Speech Synthesis Markup Language (SSML): linguaggio di markup basato su XML utilizzato per personalizzare gli output di sintesi vocale. Con SSML, è possibile regolare l'altezza, aggiungere pause, migliorare la pronuncia, accelerare o rallentare la velocità di conversazione, aumentare o diminuire il volume e attribuire più voci a un singolo documento. Vedere [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Introduzione

Il servizio di sintesi vocale è disponibile tramite [Speech SDK.](speech-sdk.md) Sono disponibili diversi scenari comuni come guide introduttive, in varie lingue e piattaforme:There are several common scenarios available as quickstarts, in various languages and platforms:

* [Sintesi vocale in un file audio](quickstarts/text-to-speech-audio-file.md)
* [Sintesi vocale in un altoparlante](quickstarts/text-to-speech.md)
* [Sintetizzare in modo asincrono l'audio in formato lungo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Se si preferisce, il servizio di sintesi vocale è accessibile tramite [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la sintesi vocale è disponibile in GitHub.Sample code for text-to-speech is available on GitHub. Questi esempi trattano la conversione da testo a voce nei linguaggi di programmazione più diffusi.

- [Esempi di sintesi vocale (SDK)Text-to-speech samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="customization"></a>Personalizzazione

Oltre alle voci standard e neurali, è possibile creare e perfezionare le voci personalizzate uniche per il prodotto o il marchio. Tutto ciò che serve per iniziare sono una manciata di file audio e le trascrizioni associate. Per altre informazioni, vedere [Introduzione a Custom Voice](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota sui prezzi

Quando si utilizza il servizio di sintesi vocale, viene addebitato ogni carattere convertito in riconoscimento vocale, inclusa la punteggiatura. Mentre il documento SSML stesso non è fatturabile, gli elementi facoltativi che vengono utilizzati per regolare il modo in cui il testo viene convertito in voce, come fonemi e passo, vengono conteggiati come caratteri fatturabili. Ecco un elenco di ciò che è fatturabile:

- Testo passato al servizio di sintesi vocale nel corpo SSML della richiesta
- Tutto il markup all'interno del campo di testo del `<speak>` corpo `<voice>` della richiesta nel formato SSML, ad eccezione dei tag e
- Lettere, punteggiatura, spazi, tabulazioni, markup e tutti gli spazi vuoti
- Ogni elemento di codice definito in Unicode

Per informazioni dettagliate, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ogni carattere di lingua cinese, giapponese e coreana viene conteggiato come due caratteri per la fatturazione.

## <a name="reference-docs"></a>Documentazione di riferimento

- [Speech SDK](speech-sdk.md)
- [API REST: Sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere un abbonamento gratuito al servizio di riconoscimento vocaleGet a free Speech service subscription](get-started.md)
- [Ottenere Speech SDK](speech-sdk.md)
