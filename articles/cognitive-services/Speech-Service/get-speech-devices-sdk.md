---
title: Ottenere Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento vocale funziona con un'ampia gamma di dispositivi e sorgenti audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. In questo articolo verrà illustrato come ottenere l'accesso all'SDK dei dispositivi di riconoscimento vocale e iniziare a sviluppare.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 756ec976c4643c1cd80552b7fa552b70fd1f5d24
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391260"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Ottenere Speech Devices SDK di Servizi cognitivi

Speech Devices SDK è una libreria pretuned progettata per funzionare con kit di sviluppo appositamente progettati e diverse configurazioni di array di microfoni.

## <a name="choose-a-development-kit"></a>Scegli un kit di sviluppo

|Dispositivi|Specifiche|Descrizione|Scenari|
|--|--|--|--|
|[URbetter T11 Development Board](https://www.alibaba.com/product-detail/URBETTER-Dev-Kits-for-Microsoft-Speech_62358746402.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, Fotocamera USB. <br>Linux|SDK di dispositivi vocali a livello di settore che adatta l'array Microsoft Mic e supporta l'I/O esteso, ad esempio HDMI/Ethernet e più periferiche USB|Trascrizione della conversazione, Istruzione, Ospedale, Robot, OTT Box, Agente vocale, Drive Thru|
|[Kit di sviluppo audio intelligente Roobo](https://ddk.roobo.com)<br>[Guida introduttiva](speech-devices-sdk-roobo-v1.md) / [Quickstart](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array, ARM SOC, WIFI, Audio Out, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|Il primo SDK di Speech Devices per adattare Microsoft Mic Array e l'SDK di elaborazione frontale, per lo sviluppo di scenari di trascrizione e riconoscimento vocale di alta qualità|Trascrizione conversazione, Smart Speaker, Voice Agent, Wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Guida](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [introduttiva](speech-devices-sdk-windows-quickstart.md)![Di Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Telecamere RGB e Profondità dell'array di microfoni. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Un kit di sviluppo con sensori avanzati di intelligenza artificiale (AI) per la creazione di sofisticati modelli di visione artificiale e di linguaggio. Combina un array di microfoni spaziali di prim'insieme e una telecamera di profondità con una videocamera e un sensore di orientamento, il tutto in un unico piccolo dispositivo con più modalità, opzioni e SDK per ospitare una gamma di tipi di calcolo.|Trascrizione delle conversazioni, Robotica, Edificio intelligente|
|Kit di sviluppo audio intelligente Roobo 2<br>[Configurazione](speech-devices-sdk-roobo-v2.md)<br>![Kit di sviluppo audio intelligente Roobo 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|L'SDK dei dispositivi vocali di seconda generazione che fornisce un sistema operativo alternativo e più funzionalità in una progettazione di riferimento conveniente.|Trascrizione conversazione, Smart Speaker, Voice Agent, Wearable|


## <a name="download-the-speech-devices-sdk"></a>Scaricare Speech Devices SDK

Scaricare [l'SDK dei dispositivi vocali](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione a Speech Devices SDK](https://aka.ms/sdsdk-quickstart)
