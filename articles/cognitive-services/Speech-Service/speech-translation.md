---
title: Traduzione vocale con il servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento vocale consente di aggiungere la traduzione end-to-end, in tempo reale e multilingua del riconoscimento vocale alle applicazioni, agli strumenti e ai dispositivi. La stessa API può essere usata sia per la traduzione vocale che per la traduzione con riconoscimento vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052611"
---
# <a name="what-is-speech-translation"></a>Che cos'è la traduzione vocale?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

La traduzione vocale dal servizio di riconoscimento vocale consente la traduzione vocale e di sintesi vocale in tempo reale dei flussi audio in tempo reale. Con Speech SDK, le applicazioni, gli strumenti e i dispositivi hanno accesso alle trascrizioni di origine e agli output di traduzione per l'audio fornito. I risultati della trascrizione e della traduzione intermedia vengono restituiti quando viene rilevato il riconoscimento vocale e i risultati delle finali possono essere convertiti in sintesi vocale.

Il motore di traduzione di Microsoft è alimentato da due approcci diversi: sminare la traduzione automatica statistica (SMT) e la traduzione automatica neurale (NMT). SMT utilizza l'analisi statistica avanzata per stimare le migliori traduzioni possibili dato il contesto di poche parole. Con NMT, le reti neurali vengono utilizzate per fornire traduzioni più accurate e dal suono naturale utilizzando il contesto completo delle frasi per tradurre le parole.

Oggi, Microsoft utilizza NMT per la traduzione nelle lingue più popolari. Tutte le [lingue disponibili per la traduzione vocale](language-support.md#speech-translation) usano la tecnologia basata sulla traduzione automatica neurale. La traduzione con riconoscimento vocale può usare la traduzione automatica statistica o la traduzione automatica neurale a seconda della coppia di lingue. Quando la lingua di destinazione è supportata da NMT, la traduzione completa è basata su NMT. Quando la lingua di destinazione non è supportata da NMT, la traduzione è un ibrido di NMT e SMT, utilizzando l'inglese come "pivot" tra le due lingue.

## <a name="core-features"></a>Caratteristiche principali

Ecco le funzionalità disponibili tramite l'SDK di riconoscimento vocale e le API REST:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Traduzione vocale con risultati di riconoscimento. | Sì | No |
| Traduzione speech-to-speech. | Sì | No |
| Riconoscimento provvisorio e risultati della traduzione. | Sì | No |

## <a name="get-started-with-speech-translation"></a>Introduzione alla traduzione vocale

Offriamo guide introduttive progettate per avere l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive per la traduzione vocale organizzate per lingua.

| Guida introduttiva | Piattaforma | API (riferimento) |
|------------|----------|---------------|
| [C'è, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | WINDOWS | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| [C'è, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | WINDOWS | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| [C,, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | WINDOWS | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | WINDOWS | [Sfoglia](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Sfoglia](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per Speech SDK è disponibile su GitHub.Sample code for the Speech SDK is available on GitHub. Questi esempi illustrano scenari comuni come la lettura dell'audio da un file o un flusso, il riconoscimento/traduzione continuo e a colpo singolo e l'utilizzo di modelli personalizzati.

* [Esempi di sintesi vocale e traduzione (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guide alla migrazione

Se le applicazioni, gli strumenti o i prodotti utilizzano [l'API Traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.

* [Eseguire la migrazione dall'API di riconoscimento vocale del traduttore al servizio di riconoscimento vocaleMigrate from the Translator Speech API to the Speech service](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: sintesi vocale: sintesi vocale](rest-speech-to-text.md)
* [API REST: sintesi vocale](rest-text-to-speech.md)
* [API REST: trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Ottenere Speech SDK](speech-sdk.md)
