---
title: Avvio rapido sulla sintesi vocale - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Speech SDK per convertire il testo in voce. Questo argomento di avvio rapido illustra la costruzione di oggetti e i modelli di progettazione, i formati di output audio supportati, l'interfaccia della riga di comando di Voce e le opzioni di configurazione personalizzate per la sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: 12ee7fbbd7699d09deae85971ffe411e115609ec
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934694"
---
# <a name="get-started-with-speech-synthesis"></a>Introduzione alla sintesi vocale

Questo argomento di avvio rapido illustra i modelli di progettazione comuni per eseguire la sintesi vocale con Speech SDK. Si inizia con la configurazione di base e la sintesi e si passa ad esempi più avanzati per lo sviluppo di applicazioni personalizzate, tra cui:

* Recupero di risposte come flussi in memoria
* Personalizzazione della frequenza di campionamento e della velocità in bit dell'output
* Invio di richieste di sintesi tramite SSML (Speech Synthesis Markup Language)
* Uso di voci neurali

> [!TIP]
> Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) in GitHub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Voce personalizzata](how-to-custom-voice.md)
* [Migliorare la sintesi con SSML](speech-synthesis-markup.md)
* Informazioni su come usare l'[API Long Audio](long-audio-api.md) per esempi di testo di grandi dimensioni, come libri e notizie
* Vedere gli [esempi di avvio rapido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) in GitHub
