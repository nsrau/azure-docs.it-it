---
title: Traduzione vocale con Speech SDK per Java
titleSuffix: Azure Cognitive Services
description: Questo articolo include un codice di esempio per la traduzione vocale con Speech SDK in un ambiente Java.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: ba0ffff6836228cbee3dc7ec0e3f2213b3044ad4
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226197"
---
# <a name="translate-speech-with-the-speech-sdk-for-java"></a>Traduzione vocale con Speech SDK per Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-java [Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-java [Translation from file input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Cercare il codice usato in questo articolo nella cartella samples/java/jre/console.

## <a name="next-steps"></a>Passaggi successivi

- [Riconoscimento vocale](how-to-recognize-speech-java.md)
- [Come riconoscere le finalità dai contenuti vocali](how-to-recognize-intents-from-speech-java.md)
