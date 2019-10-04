---
title: Informazioni sull'SDK dei dispositivi vocali-servizio riconoscimento vocale
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
ms.openlocfilehash: f2819dd2194193ffa8171034fdbe01ac4cb26174
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558920"
---
# <a name="about-the-speech-devices-sdk"></a>Informazioni su Speech Devices SDK

I [servizi di riconoscimento vocale](overview.md) funzionano con una vasta gamma di dispositivi e origini audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. Speech Devices SDK è una libreria pre-ottimizzata abbinata a kit dedicati per lo sviluppo di matrici di microfoni.

Speech Devices SDK consente di:

* Testare rapidamente nuovi scenari vocali.
* Integra in modo più semplice i servizi di riconoscimento vocale basati sul cloud nel tuo dispositivo.
* Creare un'esperienza utente eccezionale per i clienti.

Speech Devices SDK è integrato con [Speech SDK](speech-sdk.md). Usa l'SDK di riconoscimento vocale per inviare l'audio elaborato dall'algoritmo di elaborazione audio avanzata dall'array microfonico del dispositivo ai servizi di [riconoscimento vocale](overview.md). Usa inoltre l'audio multicanale per fornire un servizio più preciso di [riconoscimento vocale](speech-to-text.md) in campo lontano tramite eliminazione del rumore e dell'eco, beamforming e riduzione dell'effetto riverbero.

È anche possibile usare l'SDK per i dispositivi vocali per compilare dispositivi di ambiente con una parola di riattivazione [personalizzata](speech-devices-sdk-create-kws.md) , in modo che il suggerimento che avvia un'interazione utente sia univoco per il marchio.

Speech Devices SDK semplifica un'ampia gamma di scenari abilitati per la voce, ad esempio gli [assistenti virtuali personalizzati per la prima voce](https://aka.ms/bots/speech/va), i sistemi di ordinamento tramite unità, la trascrizione delle [conversazioni](conversation-transcription-service.md)e gli smart speaker. È possibile rispondere agli utenti con testo, parlare con loro con una voce predefinita o [personalizzata](how-to-customize-voice-font.md), fornire risultati di ricerca, [tradurre](speech-translation.md) in altre lingue e altro ancora. Microsoft sarà lieta di ricevere notizie sulle soluzioni vocali realizzate dai propri utenti.

## <a name="get-the-speech-devices-sdk"></a>Ottenere Speech Devices SDK

### <a name="android"></a>Android

Per i dispositivi Android scaricare la versione più recente di [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Per Windows, l'applicazione di esempio viene fornita come applicazione Java multipiattaforma. Scaricare la versione più recente di [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
L'applicazione è compilata con il pacchetto Speech SDK ed Eclipse Java IDE (v4) in Windows a 64 bit. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

### <a name="linux"></a>Linux

Per Linux, l'applicazione di esempio viene fornita come applicazione Java multipiattaforma. Scaricare la versione più recente di [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
L'applicazione è compilata con il pacchetto Speech SDK ed Eclipse Java IDE (v4.64) su Linux a 64 bit (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere il dispositivo vocale](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
