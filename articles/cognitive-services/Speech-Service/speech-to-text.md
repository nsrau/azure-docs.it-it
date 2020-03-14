---
title: Servizio riconoscimento vocale vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di riconoscimento vocale consente la trascrizione in tempo reale dei flussi audio in testo. Le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare ed eseguire azioni su questo input di testo. Questo servizio funziona senza interruzioni con le funzionalità di sintesi vocale e traduzione vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219544"
---
# <a name="what-is-speech-to-text"></a>Che cos'è il riconoscimento vocale?

Il riconoscimento vocale del servizio riconoscimento vocale, noto anche come riconoscimento vocale, consente la trascrizione in tempo reale dei flussi audio in testo. Le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare ed eseguire operazioni su questo testo come input del comando. Questo servizio è basato sulla stessa tecnologia di riconoscimento utilizzata da Microsoft per i prodotti Cortana e Office. Funziona senza interruzioni con le offerte di servizi di <a href="./speech-translation.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> traduzione</a> e <a href="./text-to-speech.md" target="_blank">sintesi vocale <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Per un elenco completo delle lingue di sintesi vocale disponibili, vedere [linguaggi supportati](language-support.md#speech-to-text).

Per impostazione predefinita, il servizio di sintesi vocale usa il modello di linguaggio universale. Questo modello è stato sottoposto a training usando i dati di proprietà di Microsoft e viene distribuito nel cloud. È ideale per gli scenari di conversazione e di dettatura. Quando si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, di lingua e di pronuncia personalizzati. La personalizzazione è utile per risolvere il rumore di ambiente o il vocabolario specifico del settore.

> [!NOTE]
> Il riconoscimento vocale Bing è stato ritirato il 15 ottobre 2019. Se le applicazioni, gli strumenti o i prodotti usano le API riconoscimento vocale Bing o Riconoscimento vocale personalizzato, sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.
> - [Eseguire la migrazione dal riconoscimento vocale Bing al servizio riconoscimento vocale](how-to-migrate-from-bing-speech.md)
> - [Eseguire la migrazione da Riconoscimento vocale personalizzato al servizio riconoscimento vocale](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>Introduzione alla sintesi vocale

Il servizio riconoscimento vocale è disponibile tramite l' [SDK di riconoscimento vocale](speech-sdk.md). Sono disponibili diversi scenari comuni come guide introduttive, in diversi linguaggi e piattaforme:

 - [Guida introduttiva: riconoscere il riconoscimento vocale con input microfono](quickstarts/speech-to-text-from-microphone.md)
 - [Guida introduttiva: riconoscere il riconoscimento vocale da un file](quickstarts/speech-to-text-from-file.md)
 - [Guida introduttiva: riconoscere il riconoscimento vocale archiviato nell'archivio BLOB](quickstarts/from-blob.md)

Se si preferisce usare il servizio REST di riconoscimento vocale, vedere [API REST](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Esercitazioni e codice di esempio

Dopo aver usato il servizio Voce, provare l'esercitazione che illustra come riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS.

- [Esercitazione: riconoscere gli Intent dalla sintesi vocale con l'SDK vocale e LUIS, usandoC#](how-to-recognize-intents-from-speech-csharp.md)

Il codice di esempio per l'SDK di riconoscimento vocale è disponibile su GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati.

- [Esempi di riconoscimento vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))

## <a name="customization"></a>Personalizzazione

Oltre al modello di servizio vocale standard, è possibile creare modelli personalizzati. La personalizzazione consente di superare gli ostacoli al riconoscimento vocale, ad esempio lo stile di pronuncia, il vocabolario e il rumore di fondo, vedere [riconoscimento vocale personalizzato](how-to-custom-speech.md). Opzioni di personalizzazione variano in base a lingua/impostazioni locali, vedere le [lingue supportate](supported-languages.md) per verificare il supporto.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
- [Ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
