---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943725"
---
La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari GStreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Al contrario, una libreria wrapper che contiene queste funzioni deve essere compilata e fornita con le app che usano l'SDK.

Per compilare questa libreria wrapper, scaricare e installare prima l' [SDK di GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Scaricare quindi il progetto **Xcode** per la [libreria wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Aprire il progetto in **Xcode** e compilarlo per la destinazione del **dispositivo iOS generico** . *non* funzionerà per la compilazione per una destinazione specifica.

L'istruzione di compilazione genererà un bundle di Framework dinamico con una libreria dinamica per tutte le architetture necessarie con il nome `GStreamerWrapper.framework`.

Questo Framework deve essere incluso in tutte le app che usano flussi audio compressi con l'SDK del servizio di riconoscimento vocale.

Per eseguire questa operazione, applicare le impostazioni seguenti nel progetto **Xcode** :

1. Copiare il `GStreamerWrapper.framework` appena compilato e il Framework di cognitive Services Speech SDK, che è possibile scaricare da [qui](https://aka.ms/csspeech/iosbinary), alla directory contenente il progetto di esempio.
1. Modificare i percorsi dei Framework nelle *impostazioni del progetto*.
   1. Nella scheda **generale** , sotto l'intestazione dei **file binari incorporati** , aggiungere la libreria SDK come Framework: **aggiungi file binari incorporati** > **Aggiungi altro...** > passare alla directory scelta e selezionare entrambi i Framework.
   1. Andare alla scheda **Impostazioni di compilazione** e attivare le impostazioni **Tutte**.
1. Aggiungere la directory `$(SRCROOT)/..` ai _Percorsi di ricerca Framework_ sotto l'intestazione **Percorsi di ricerca**.
