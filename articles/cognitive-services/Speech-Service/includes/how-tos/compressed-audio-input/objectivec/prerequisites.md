---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943725"
---
La gestione dell'audio compresso viene implementata utilizzando [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza, i file binari GStreamer non vengono compilati e collegati a Speech SDK. Al contrario, una libreria wrapper contenente queste funzioni deve essere compilata e fornita con le app usando l'SDK.

Per compilare questa libreria wrapper, scaricare e installare [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Quindi, scaricare il progetto **Xcode** per la [libreria wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Aprire il progetto in **Xcode** e compilarlo per la destinazione **dispositivo iOS generico** - *non* funzionerà per compilarlo per una destinazione specifica.

L'istruzione di compilazione genererà un pacchetto del framework dinamico `GStreamerWrapper.framework`con una libreria dinamica per tutte le architetture necessarie con il nome di .

Questo framework deve essere incluso in tutte le app che usano flussi audio compressi con l'SDK del servizio di riconoscimento vocale.

A tale scopo, applicare le seguenti impostazioni nel progetto **Xcode:**

1. Copiare `GStreamerWrapper.framework` l'SDK di riconoscimento vocale di Servizi cognitivi appena compilato e il framework dell'SDK di riconoscimento vocale di Servizi cognitivi, che è possibile scaricare da [qui,](https://aka.ms/csspeech/iosbinary)nella directory contenente il progetto di esempio.
1. Modificare i percorsi ai framework in *Impostazioni progetto*.
   1. Nella scheda **Generale** dell'intestazione **Binari incorporati** aggiungere la libreria SDK come framework: Aggiungi file binari incorporati Aggiungi altri file binari Aggiungere **altri...** > **Add other...** > passare alla directory selezionata e selezionare entrambi i framework.
   1. Andare alla scheda **Impostazioni di compilazione** e attivare le impostazioni **Tutte**.
1. Aggiungere la directory `$(SRCROOT)/..` ai _Percorsi di ricerca Framework_ sotto l'intestazione **Percorsi di ricerca**.
