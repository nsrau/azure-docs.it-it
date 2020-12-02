---
title: Panoramica di sintesi vocale-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il software di riconoscimento vocale consente la trascrizione in tempo reale dei flussi audio in testo. Le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare ed eseguire azioni su questo input di testo. Questo articolo offre una panoramica dei vantaggi e delle funzionalità del servizio di riconoscimento vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: riconoscimento vocale, software di riconoscimento vocale
ms.openlocfilehash: 102289fd233a744952f37568abf77a753953637e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499646"
---
# <a name="what-is-speech-to-text"></a>Che cos'è il riconoscimento vocale?

In questa panoramica vengono illustrati i vantaggi e le funzionalità del servizio di riconoscimento vocale.
La sintesi vocale, nota anche come riconoscimento vocale, consente la trascrizione in tempo reale dei flussi audio in testo. Le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare ed eseguire operazioni su questo testo come input del comando. Questo servizio è basato sulla stessa tecnologia di riconoscimento utilizzata da Microsoft per i prodotti Cortana e Office. Funziona senza interruzioni con le offerte di servizi di <a href="./speech-translation.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> traduzione</a> e <a href="./text-to-speech.md" target="_blank">sintesi vocale <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Per un elenco completo delle lingue di sintesi vocale disponibili, vedere [linguaggi supportati](language-support.md#speech-to-text).

Per impostazione predefinita, il servizio di sintesi vocale usa il modello di linguaggio universale. Questo modello è stato sottoposto a training usando i dati di proprietà di Microsoft e viene distribuito nel cloud. È ideale per gli scenari di conversazione e di dettatura. Quando si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, di lingua e di pronuncia personalizzati. La personalizzazione è utile per risolvere il rumore di ambiente o il vocabolario specifico del settore.

Con il testo di riferimento aggiuntivo come input, il servizio riconoscimento vocale consente inoltre la funzionalità di [valutazione della pronuncia](rest-speech-to-text.md#pronunciation-assessment-parameters) per valutare la pronuncia vocale e fornire commenti e suggerimenti sull'accuratezza e la fluidità dell'audio parlato. Con la valutazione della pronuncia, gli Learner del linguaggio possono esercitarsi, ottenere commenti e suggerimenti istantanei e migliorare la loro pronuncia in modo che possano comunicare e presentare in tutta sicurezza. Gli educatori possono usare la funzionalità per valutare la pronuncia di più speaker in tempo reale. La funzionalità supporta attualmente la lingua inglese (Stati Uniti) e mette in correlazione le valutazioni dei dialoghi eseguite dagli esperti.

> [!NOTE]
> Il riconoscimento vocale Bing è stato ritirato il 15 ottobre 2019. Se le applicazioni, gli strumenti o i prodotti usano le API riconoscimento vocale Bing, sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.
> - [Eseguire la migrazione dal riconoscimento vocale Bing al servizio riconoscimento vocale](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Introduzione

Per iniziare a usare la sintesi vocale, vedere la [Guida introduttiva](get-started-speech-to-text.md) . Il servizio è disponibile tramite l' [SDK vocale](speech-sdk.md), l' [API REST](rest-speech-to-text.md#pronunciation-assessment-parameters)e l'interfaccia della riga di comando [vocale](spx-overview.md).

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per l'SDK di riconoscimento vocale è disponibile su GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati.

- [Esempi di riconoscimento vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
- [Esempi di valutazione della pronuncia (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalizzazione

Oltre al modello di servizio vocale standard, è possibile creare modelli personalizzati. La personalizzazione consente di superare gli ostacoli al riconoscimento vocale, ad esempio lo stile di pronuncia, il vocabolario e il rumore di fondo, vedere [riconoscimento vocale personalizzato](./custom-speech-overview.md). Opzioni di personalizzazione variano in base a lingua/impostazioni locali, vedere le [lingue supportate](./language-support.md) per verificare il supporto.

## <a name="batch-transcription"></a>Trascrizione Batch

La trascrizione batch è un set di operazioni API REST che consentono di trascrivere una grande quantità di audio nell'archivio. È possibile puntare a file audio con un URI di firma di accesso condiviso (SAS) e ricevere in modo asincrono i risultati della trascrizione. Vedere le [procedure](batch-transcription.md) per altre informazioni su come usare l'API di trascrizione batch.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](overview.md#try-the-speech-service-for-free)
- [Ottenere Speech SDK](speech-sdk.md)