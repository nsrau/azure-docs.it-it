---
title: Informazioni su Speech Devices SDK - Servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Introduzione a Speech Devices SDK. Il servizio Voce è compatibile con una vasta gamma di dispositivi e sorgenti audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. Speech Devices SDK è una libreria pre-ottimizzata abbinata a kit dedicati per lo sviluppo di matrici di microfoni.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718536"
---
# <a name="about-the-speech-devices-sdk"></a>Informazioni su Speech Devices SDK

Il [servizi di riconoscimento vocale](overview.md) funzionano con un'ampia gamma di dispositivi e origini audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. Speech Devices SDK è una libreria pre-ottimizzata abbinata a kit dedicati per lo sviluppo di matrici di microfoni.

Speech Devices SDK consente di:

* Testare rapidamente nuovi scenari vocali.
* Integrare più facilmente i servizi di riconoscimento vocale basati sul cloud al dispositivo.
* Creare un'esperienza utente eccezionale per i clienti.

Speech Devices SDK è integrato con [Speech SDK](speech-sdk.md). Usa Speech SDK per inviare audio elaborato dal nostro algoritmo di elaborazione audio avanzata dalla matrice di microfono del dispositivo per il [servizi di riconoscimento vocale](overview.md). Usa inoltre l'audio multicanale per fornire un servizio più preciso di [riconoscimento vocale](speech-to-text.md) in campo lontano tramite eliminazione del rumore e dell'eco, beamforming e riduzione dell'effetto riverbero.

È possibile usare anche il SDK di dispositivi di riconoscimento vocale per creare dispositivi ambiente con il proprio [personalizzare word riattivazione](speech-devices-sdk-create-kws.md) in modo che il segnale che avvia un'interazione utente è univoco per il tuo marchio.

Il riconoscimento vocale Devices SDK facilita un'ampia gamma di scenari di riconoscimento vocale, ad esempio [assistenti virtuali Voice-First personalizzate](https://aka.ms/bots/speech/va), unità-through, i sistemi di ordinamento [trascrizione conversazione](conversation-transcription-service.md)e smart relatori. È possibile rispondere agli utenti con testo, parlare con loro con una voce predefinita o [personalizzata](how-to-customize-voice-font.md), fornire risultati di ricerca, [tradurre](speech-translation.md) in altre lingue e altro ancora. Microsoft sarà lieta di ricevere notizie sulle soluzioni vocali realizzate dai propri utenti.

## <a name="get-the-speech-devices-sdk"></a>Ottenere Speech Devices SDK

### <a name="android"></a>Android

Per Android dispositivi scaricare la versione più recente del [Android SDK di dispositivi di riconoscimento vocale](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Per Windows, l'applicazione di esempio viene fornita come un'applicazione Java di multi-piattaforma. Scaricare la versione più recente del [JRE vocale Devices SDK](https://aka.ms/sdsdk-download-JRE).
L'applicazione viene compilata con il pacchetto di Speech SDK e l'IDE di Java (v4) di Eclipse su Windows a 64 bit. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

### <a name="linux"></a>Linux

Per Linux viene fornita l'applicazione di esempio come un'applicazione Java di multi-piattaforma. Scaricare la versione più recente del [JRE vocale Devices SDK](https://aka.ms/sdsdk-download-JRE).
L'applicazione viene compilata con il pacchetto di Speech SDK e l'IDE di Java (v4) di Eclipse in Linux a 64 bit (Ubuntu 16.04, 18.04 Ubuntu, Debian 9). Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere il dispositivo di riconoscimento vocale](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
