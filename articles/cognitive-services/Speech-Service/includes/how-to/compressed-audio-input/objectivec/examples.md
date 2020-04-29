---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422186"
---
Per trasmettere in streaming un formato audio compresso al servizio di riconoscimento vocale, `SPXPullAudioInputStream` creare `SPXPushAudioInputStream`un oggetto o.

Nel frammento di codice seguente viene illustrato `SPXAudioConfiguration` come creare un oggetto da `SPXPushAudioInputStream`un'istanza di un oggetto, specificando un file MP3 come formato di compressione del flusso.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Il frammento di codice successivo Mostra come i dati audio compressi possono essere letti da un file e `SPXPushAudioInputStream`inseriti in.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
