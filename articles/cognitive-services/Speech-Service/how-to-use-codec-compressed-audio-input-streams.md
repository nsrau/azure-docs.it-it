---
title: Flusso audio compresso di codec con l'SDK vocale-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come trasmettere l'audio compresso al servizio riconoscimento vocale con l'SDK di riconoscimento vocale. Disponibile per C++, C#e Java per Linux, Java in Android e Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943857"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Usare l'input audio compresso di codec con l'SDK di riconoscimento vocale

L'API del **flusso di input audio compresso** dell'SDK di riconoscimento vocale fornisce un modo per trasmettere audio compresso al servizio di riconoscimento vocale usando un `PullStream` o `PushStream`.

> [!IMPORTANT]
> Il flusso audio di input compresso è attualmente C#supportato C++per,, Java in Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). È supportata anche per Java in Android e Objective-C nella piattaforma iOS.
> La versione 1.7.0 o successiva dell'SDK vocale è obbligatoria (versione 1.10.0 o successiva per RHEL 8, CentOS 8).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Prerequisites

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio con input audio compresso codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come riconoscere il riconoscimento vocale](quickstarts/speech-to-text-from-microphone.md)
