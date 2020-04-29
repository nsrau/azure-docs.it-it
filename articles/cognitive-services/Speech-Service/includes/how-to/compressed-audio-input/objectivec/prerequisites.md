---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421691"
---
La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari GStreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Al contrario, una libreria wrapper che contiene queste funzioni deve essere compilata e fornita con le app che usano l'SDK.

Per compilare questa libreria wrapper, scaricare e installare prima l' [SDK di GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Scaricare quindi il progetto **Xcode** per la [libreria wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Aprire il progetto in **Xcode** e compilarlo per la destinazione del **dispositivo iOS generico** . *non* funzionerà per la compilazione per una destinazione specifica.

L'istruzione di `GStreamerWrapper.framework`compilazione genererà un bundle di Framework dinamico con una libreria dinamica per tutte le architetture necessarie con il nome.

Questo Framework deve essere incluso in tutte le app che usano flussi audio compressi con l'SDK del servizio di riconoscimento vocale.

Per eseguire questa operazione, applicare le impostazioni seguenti nel progetto **Xcode** :

1. Copiare la `GStreamerWrapper.framework` classe appena creata e il Framework di cognitive Services Speech SDK, che è possibile scaricare da [qui](https://aka.ms/csspeech/iosbinary), alla directory contenente il progetto di esempio.
1. Modificare i percorsi dei Framework nelle *impostazioni del progetto*.
   1. Nella scheda **generale** , sotto l'intestazione dei **file binari incorporati** , aggiungere la libreria SDK come Framework: >  **Aggiungi file binari incorporati****Aggiungi altro...** > passare alla directory scelta e selezionare entrambi i Framework.
   1. Andare alla scheda **Impostazioni di compilazione** e attivare le impostazioni **Tutte**.
1. Aggiungere la directory `$(SRCROOT)/..` ai _Percorsi di ricerca Framework_ sotto l'intestazione **Percorsi di ricerca**.
