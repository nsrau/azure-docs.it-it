---
title: Traduzione vocale con i servizi di riconoscimento vocale di Azure
titlesuffix: Azure Cognitive Services
description: I servizi voce consentono di aggiungere end-to-end, in tempo reale, in più lingue la traduzione vocale per le applicazioni, strumenti e i dispositivi. La stessa API può essere usata sia per la traduzione vocale che per la traduzione con riconoscimento vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 20e47b6d3243bb0cccbc42ab0ab904b72922e98b
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604789"
---
# <a name="what-is-speech-translation"></a>Che cos'è la traduzione vocale?

API traduzione vocale di servizi di riconoscimento vocale, Azure Abilita traduzione vocale vocale e riconoscimento vocale in tempo reale, multilingue di flussi audio. Con il SDK di riconoscimento vocale, applicazioni, strumenti e i dispositivi hanno accesso a trascrizioni di origine e di output di traduzione per l'audio specificato. I risultati intermedi di trascrizione e traduzione vengono restituiti quando viene rilevato il parlato, e risultati finali possono essere convertiti in sintesi vocale.

Motore di conversione di Microsoft si basa su due diversi approcci: traduzione automatica statistici (LAM) e traduzione automatica neurale (NMT). SMT Usa analisi statistiche avanzate per stimare le conversioni migliore possibili nel contesto di alcune parole. Con NMT, le reti neurali vengono usate per fornire le traduzioni più accurate, naturale, usando il contesto completo di frasi di tradurre parole.

Microsoft Usa attualmente NMT per la conversione a linguaggi più diffusi. Tutte le [lingue disponibili per la traduzione vocale](language-support.md#speech-translation) usano la tecnologia basata sulla traduzione automatica neurale. La traduzione con riconoscimento vocale può usare la traduzione automatica statistica o la traduzione automatica neurale a seconda della coppia di lingue. Quando la lingua di destinazione è supportata da NMT, la traduzione completa è basato su NMT. Quando la lingua di destinazione non è supportata da NMT, la conversione è un ibrido di NMT e SMT, usando l'inglese come "pivot" tra i due linguaggi.

## <a name="core-features"></a>Funzionalità di base

Ecco le funzionalità disponibili attraverso il sistema Speech SDK e API REST:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Traduzione vocale-con risultati del riconoscimento. | Yes | No |
| Traduzione vocale vocale. | Yes | No |
| Risultati di riconoscimento e traduzione intermedi. | Yes | No |

## <a name="get-started-with-speech-translation"></a>Introduzione a funzionalità di traduzione vocale

Sono disponibili guide introduttive progettato per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco delle guide introduttive di traduzione vocale organizzati per linguaggio.

| Guida introduttiva | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| [C#, .NET core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Codice di esempio per Speech SDK è disponibile in GitHub. Questi esempi coprono scenari comuni, ad esempio la lettura di audio da un file o flusso, continua e unica riconoscimento/traduzione e l'utilizzo di modelli personalizzati.

* [Esempi di riconoscimento vocale e traduzione (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guide alla migrazione

> [!WARNING]
> Traduzione vocale Microsoft Translator verrà ritirato il 15 ottobre 2019.

Se le applicazioni, strumenti o prodotti utilizza traduzione vocale Microsoft Translator, abbiamo creato le guide per facilitare la migrazione ai servizi di riconoscimento vocale.

* [Eseguire la migrazione dall'API traduzione vocale Microsoft Translator per i servizi di riconoscimento vocale](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
* [Ottenere il SDK di riconoscimento vocale](speech-sdk.md)
