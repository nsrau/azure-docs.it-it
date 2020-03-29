---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943783"
---
Per trasmettere in un formato audio compresso `SPXPullAudioInputStream` `SPXPushAudioInputStream`al servizio di riconoscimento vocale, creare un o .

Nel frammento di codice `SPXAudioConfiguration` seguente viene `SPXPushAudioInputStream`illustrato come creare un da un'istanza di un oggetto , specificando un MP3 come formato di compressione del flusso.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Il frammento successivo mostra come i dati audio compressi `SPXPushAudioInputStream`possono essere letti da un file e pompati nel file .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
