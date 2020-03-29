---
title: Sintesi vocale - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di sintesi vocale consente la trascrizione in tempo reale dei flussi audio nel testo. Le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare ed eseguire azioni su questo input di testo. Questo servizio funziona perfettamente con le funzionalità di sintesi vocale e traduzione vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052638"
---
# <a name="what-is-speech-to-text"></a>Che cos'è il riconoscimento vocale?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

La sintesi vocale del servizio di riconoscimento vocale, nota anche come riconoscimento vocale, consente la trascrizione in tempo reale dei flussi audio nel testo. Le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare ed eseguire azioni su questo testo come input del comando. Questo servizio è alimentato dalla stessa tecnologia di riconoscimento utilizzata da Microsoft per i prodotti Cortana e Office. Funziona perfettamente con le offerte di servizi di <a href="./speech-translation.md" target="_blank">traduzione <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e <a href="./text-to-speech.md" target="_blank">di sintesi vocale. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Per un elenco completo delle lingue di sintesi vocale disponibili, vedere [Lingue supportate.](language-support.md#speech-to-text)

Il servizio di sintesi vocale utilizza per impostazione predefinita il modello di linguaggio universale. Questo modello è stato eseguito il training utilizzando dati di proprietà di Microsoft e viene distribuito nel cloud. È ottimale per scenari conversazionali e di dettatura. Quando si usa la sintesi vocale per il riconoscimento e la trascrizione in un ambiente unico, è possibile creare e addestrare modelli acustici, linguistici e di pronuncia personalizzati. La personalizzazione è utile per affrontare il rumore ambientale o il vocabolario specifico del settore.

> [!NOTE]
> Bing Speech è stato dismesso il 15 ottobre 2019. Se le applicazioni, gli strumenti o i prodotti usano le API di riconoscimento vocale Bing, sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.
> - [Eseguire la migrazione da Bing Speech al servizio di riconoscimento vocale](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Introduzione alla sintesi vocale

Il servizio di sintesi vocale è disponibile tramite [Speech SDK.](speech-sdk.md) Sono disponibili diversi scenari comuni come guide introduttive, in varie lingue e piattaforme:There are several common scenarios available as quickstarts, in various languages and platforms:

 - [Guida introduttiva: Riconoscere il riconoscimento vocale con l'input del microfono](quickstarts/speech-to-text-from-microphone.md)
 - [Guida introduttiva: Riconoscere il riconoscimento vocale da un file](quickstarts/speech-to-text-from-file.md)
 - [Guida introduttiva: Riconoscere il riconoscimento vocale archiviato nell'archiviazione BLOBQuickstart: Recognize speech stored in blob storage](quickstarts/from-blob.md)

Se si preferisce usare il servizio REST di sintesi vocale, vedere [API REST](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Esercitazioni e codice di esempio

Dopo aver usato il servizio Voce, provare l'esercitazione che illustra come riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS.

- [Esercitazione: Riconoscere le finalità del parlato con Speech SDK e LUIS, usando CTutorial: Recognize intents from speech with the Speech SDK and LUIS, using C #](how-to-recognize-intents-from-speech-csharp.md)

Il codice di esempio per Speech SDK è disponibile su GitHub.Sample code for the Speech SDK is available on GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati.

- [Esempi di sintesi vocale (SDK)Speech-to-text samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))

## <a name="customization"></a>Personalizzazione

Oltre al modello di servizio di riconoscimento vocale standard, è possibile creare modelli personalizzati. La personalizzazione aiuta a superare le barriere di riconoscimento vocale come lo stile di parlare, il vocabolario e il rumore di fondo, vedere [Riconoscimento vocale personalizzato](how-to-custom-speech.md). Le opzioni di personalizzazione variano in base alla lingua/impostazioni locali, vedere [lingue supportate](supported-languages.md) per verificare il supporto.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
- [Ottenere Speech SDK](speech-sdk.md)
