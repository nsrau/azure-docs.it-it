---
title: Flusso audio compresso di codec con l'SDK vocale-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come trasmettere l'audio compresso al servizio riconoscimento vocale con l'SDK di riconoscimento vocale. Disponibile per C++, C# e Java per Linux, Java in Android e Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: ace584b51f0aa457e30d8e48d97834aa735a1a57
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026574"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Usare l'input audio compresso di codec con l'SDK di riconoscimento vocale

L'API del **flusso di input audio compresso** dell'SDK per servizi vocali fornisce un modo per trasmettere l'audio compresso al servizio di riconoscimento vocale usando `PullStream` o `PushStream` .

Piattaforma | Linguaggi | Versione di GStreamer supportata
| :--- | ---: | :---:
Windows (escluso UWP)  | C++, C#, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C++, C#, Java, Python | [distribuzioni Linux e architetture di destinazione supportate](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Versione dell'SDK vocale necessaria per l'input audio compresso
* Speech SDK versione 1.10.0 o successiva è necessario per RHEL 8 e CentOS 8
* Per Windows è necessaria la versione 1.11.0 o successiva di Speech SDK.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio con input audio compresso codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come riconoscere il riconoscimento vocale](./get-started-speech-to-text.md)