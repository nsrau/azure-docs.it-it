---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943783"
---
Per trasmettere in streaming un formato audio compresso al servizio riconoscimento vocale, creare un `SPXPullAudioInputStream` o `SPXPushAudioInputStream`.

Nel frammento di codice seguente viene illustrato come creare un `SPXAudioConfiguration` da un'istanza di un `SPXPushAudioInputStream`, specificando un file MP3 come formato di compressione del flusso.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Il frammento di codice successivo Mostra come i dati audio compressi possono essere letti da un file e inseriti nel `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
