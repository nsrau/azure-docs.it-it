---
title: Streaming codec audio compresso con il servizio di riconoscimento vocale - riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Scopri come trasmettere l'audio compresso al servizio di riconoscimento vocale con Speech SDK. Disponibile per C 'è, C 'e Java per Linux, Java in Android e Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637285"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Usare l'input audio compresso codec con Speech SDK

L'API **Flusso di input audio compresso** dell'SDK del servizio `PullStream` di `PushStream`riconoscimento vocale consente di trasmettere l'audio compresso al servizio di riconoscimento vocale utilizzando un oggetto o .

Lo streaming dell'audio di input compresso è attualmente supportato per C'è, C ' , Java su Windows (le applicazioni UWP non sono supportate) e Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). È supportato anche per Java in Android e Objective-C nella piattaforma iOS.
* Speech SDK versione 1.10.0 o successiva è necessario per RHEL 8 e CentOS 8
* Per Windows è necessario Speech SDK versione 1.11.0 o successiva.

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

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Esempio di codice con input audio compresso codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scopri come riconoscere il parlato](quickstarts/speech-to-text-from-microphone.md)
