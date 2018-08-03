---
title: Esempio per la traduzione | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Di seguito viene illustrato un esempio per la traduzione vocale.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 66d26181334a71578f1a94000cb942a6a87398bc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070792"
---
# <a name="sample-for-translation"></a>Esempio per la traduzione

[!include[Get a Subscription Key](../../../includes/cognitive-services-speech-service-get-subscription-key.md)]

## <a name="top-level-declarations"></a>Dichiarazioni di primo livello

Per tutti gli esempi riportati di seguito devono essere presenti queste dichiarazioni di primo livello:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

## <a name="translation-using-the-microphone"></a>Traduzione con uso del microfono

Il frammento di codice riportato di seguito illustra come eseguire una traduzione vocale dall'inglese al tedesco e come ottenere l'output vocale del testo tradotto. Viene usato il microfono.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!code-cpp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#TranslationWithMicrophone)]

[!code-java [Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

## <a name="translation-using-file-input"></a>Traduzione con input di file

Il frammento di codice riportato di seguito illustra come eseguire una traduzione vocale dall'inglese al tedesco e al francese.
Viene usato un file come input.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!code-java [Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Riconoscimento vocale](./speech-to-text-sample.md)

- [Riconoscimento finalità](./intent.md)
